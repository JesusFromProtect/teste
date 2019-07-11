# Sistema para exibicao de relatorio de disponibilidade de sensores de monitoramento @ Real Protect, a.k.a. PAINEL DE INDICADORES DE DISPONIBILIDADE ou RP-PID.
## Autor: 
1. Daniel Alves

    Security Operation Center / Real Protect

    Tel: 21 2114.2312 | 21 2114.2340
    
2. Anderson de Jesus Santos

    Security Operation Center / Real Protect

    Tel: 11 4861.4003 – Ramal 3009

    Skype: anderson7408
    
3. Vinícius Andrade

    Security Operation Center / Real Protect

    Tel: 21 2114.2323

    Skype: vigreenhalgh
    
4. Rodrigo Menchio

    Security Operation Center / Real Protect

    Tel: 21 2114.2345


    

# Lista de Mudancas Principais
08/05/19 - Upload e versionamento fonte prototipo com GIT

09/07/19 - Release alfa makecatalog.py fazendo detecção de objetos de interesse no PRTG

## Criado em Fev/2019
# Arquitetura

## Estrutura do Fonte:

../             -   Diretório superior ao sistema. Raiz do servidor de páginas (geralmente C:\xampp\htdocs), e portanto fora do versionamento; onde está o arquivo "pidhash.php". Ver "Passhash"!\

<br/>

/               -   Raiz do sistema. Base do versionamento.\
index           -   Página default. Faz chamadas apenas a imagens.\
img/             -   Diretório com imagens do sistema.\
header          -   Cabeçalho HTML, que faz chamadas aos arquivos de estilo, javasccript , etc. Chamada pelas pagina index inferiores.\
footer          -   Rodapé HTML, que faz chamadas aos arquivos de estilo. Chamada pelas pagina index inferiores.\

<br/>


"EMPRESA"/      -   Diretório com arquivos relacionados ao report de uma empresa em questão, cujos arquivos abaixo são o esqueleto de cada empresa\
index           -   Página default. Faz chamadas ao header e footer do diretório superior, que são compartilhados entre todas as empresas. Além disso pode chamar outros arquivos exclusivos de cada empresa devido a especificidade de determinado relatório (ex.: switches.php)\
catalog         -   Índice onde ficam organizados os id dos sensores desejados. NÃO EDITAR - GERADO AUTOMATICAMENTE PELO SCRIPT MAKECATALOG.PY

<br/>

## Operacao:
1. Corpo técnico ou gestores acessam o Painel de Indicadores através do navegador.
2. Usuário então seleciona a competência apropriada para o relatório (Default = Último mês), e clica na empresa de que se deseja analisar, neste momento o script makecatalog.py é acionado.
3. Sistema executa uma chamada HTTP (GET) à API PRTG, que responde no formato JSON
4. Sistema constrói e exibe relatorio


## Requisitos:
+ Microsoft Visual C++ 2017 Redistributable
+ XAMPP v7.3.1 \
    * Apache 2.4
    * PHP v7.3.1
+ PRTG (Login + Passhash) - Chave restrita às cópias locais e portanto fora do versionamento. Ver "Passhash"!
+ Python 3
+ FONTE: https://github.com/dbeniciorj/RP-PID 


## DEV:
Para sincronizar o fonte, efetuar os passos abaixo:\
1. Baixe e instale o GIT BASH a partir do site  https://git-scm.com/download/win 
2. Acesse a raiz \do servidor de páginas, conforme descrito anteriormente.
3. Clone o repositório com o comando
```bash
git clone https://github.com/dbeniciorj/RP-PID
```
<br/>    
OBS: Mais instruções para operações com GIT em https://rogerdudler.github.io/git-guide/ 


## Passhash:
Autenticação do sistema na API PRTG armazenada na raiz do servidor de páginas e fora do versionamento por questão de segurança, no arquivo pidhash.php, cujo exemplo segue abaixo.\
Cada "Empresa" faz uma consulta ao arquivo com a chave de acesso, conforme código abaixo, e cujo valor em PASSHASH pode ser obtido com a seguinte chamada a API:
```
https://PRTG_hostname_or_IP_address/api/getpasshash.htm?username=myuser&password=mypass 
```

Exemplo:
```php
<?php	

	$USERNAME = "rp-pid";
	$PASSHASH = "XXXXXXXXXXXXXXXXXXXXX";
	
?>
```

## Detecção de sensores:

O script gera um arquivo chamado catalog.php, que tem por intuito montar uma interface web com o relatório dos ativos de determinada empresa, utilizando tags pré-definidas, individualmente, em cada sensor.

Atualmente, a execução do script se faz da seguinte maneira:

```bash
python makecatalog.py $empresa
```

A partir deste ponto, o script busca pelas **tags** já pré-definidas e constroi o relatório, baseado no parâmetro "empresa" que você indicar.

### Siga os procedimentos para adicionar **tags** a sensores.

1. Após acessar o PRTG, vá a página de algum item que você deseje identificar através de uma tag e clique em Settings.

![Alt Text](https://i.ibb.co/sH9znNh/settings.png)

2. Ao entrar na página, procure pela caixa “Tags” em “Basic Sensor Settings” e insira a tag que desejar. Pronto, seu sensor já tem uma tag acompanhando-o e pode ser encontrado através desta tag.

![Alt Text](https://i.ibb.co/QcK9p1w/tags.png)

Nosso script utiliza as seguintes **tags**:

|Tags|Função|
|:---|:-----|
|pidlded|Tag para encontrar sensores que são links dedicados|
|pidswitch|Tag para encontrar sensores que são switches|
|pidout|Tag para encontrar outros dispositivos|
|pidsplunk|Tag para encontrar o id do sensor do splunk|
|pidlint|Tag para encontrar sensores que são links de internet|

|Sub-Tags|Função|
|:-------|:-----|
|site:xxx|Tag para indicar a localidade dos sensores|
|isp:xxx|Tag para indicar a provedora de internet daquele sensor|
|tipodolink:xxx|Tag para indicar tipo do link|
|-|-|
|-|-|
|rows:x|Tag para encontrar o número de células unidas|
|-|-|

**Sub tags** significam tags que estão atrelados a uma **tag** principal. Por exemplo, a tag **pidlded** indica sensores de links dedicados, que por sua vez possuem mais informações, como a provedora, o tipo de link e a localidade. Estas tags também devem ser criadas para que a geração de relatório aconteça de forma eficiente. 

Para fins demonstrativos, observe a imagem abaixo, com as tags definidas para um sensor de link dedicado:

![Alt Text](https://i.ibb.co/gSGDbbM/capture.png)

Note que o sensor possui as **sub tags** chamadas *isp:algar*, *tipodolink:pap*, *site:rj-dc*, que deverão ser definidas junto com a tag *pidlded*, no processo de adição de um novo ativo.





