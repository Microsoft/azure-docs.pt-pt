---
title: 'Tutorial: Lance o leitor imersivo usando Python'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai criar uma aplicação Python que lança o Leitor Imersivo.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.openlocfilehash: a252afae0a007ee0b791b56d19ffb0685848d30a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76844365"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Tutorial: Lance o Leitor Imersivo utilizando o projeto de amostra Python

Na [visão geral,](./overview.md)aprendeu sobre o que é o Leitor Imersivo e como implementa técnicas comprovadas para melhorar a compreensão da leitura para os alunos de línguas, leitores emergentes e alunos com diferenças de aprendizagem. Este tutorial cobre como criar uma aplicação web Python que lança o Leitor Imersivo. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma aplicação web Python com Pip, Flask, Jinja e virtualenv usando um projeto de amostra
> * Adquirir um token de acesso
> * Lançar o Leitor Imersivo com conteúdo de amostra

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso Imersivo do Leitor configurado para autenticação de Diretório Ativo Azure. Siga [estas instruções](./how-to-create-immersive-reader.md) para ser configurado. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do ambiente. Guarde a saída da sua sessão num ficheiro de texto para referência futura.
* [Git](https://git-scm.com/)
* [Leitor Imersivo SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) e [pip.](https://docs.python.org/3/installing/index.html) Começando com Python 3.4, o pip é incluído por padrão com os instaladores binários Python.
* [Balão](https://flask.palletsprojects.com/en/1.0.x/)
* [Rio Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) e [virtualenvwrapper-win para Windows](https://pypi.org/project/virtualenvwrapper-win/) ou [virtualenvwrapper para OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [módulo de pedidos](https://pypi.org/project/requests/2.7.0/)
* Um IDE como [Visual Studio Code](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>Configure credenciais de autenticação

Crie um novo ficheiro chamado _.env_, e colhe o seguinte código nele, fornecendo os valores dados quando criou o seu recurso Imersivo leitor.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Certifique-se de não comprometer este ficheiro no controlo de fontes, uma vez que contém segredos que não devem ser tornados públicos.

O ponto final da API **getimeriken** deve ser protegido por detrás de alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que utilizadores não autorizados obtenham fichas para usar contra o seu serviço de Leitor Imersivo e faturação; que o trabalho está fora do âmbito deste tutorial.

## <a name="create-a-python-web-app-on-windows"></a>Criar uma aplicação web Python no Windows

Crie uma aplicação web Python utilizando `flask` no Windows.

Instale [Git](https://git-scm.com/).

Depois de git ser instalado abra um Pedido de Comando e 'clone' o repositório Imersivo Do Leitor SDK Git para uma pasta no seu computador

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Instale [Python.](https://www.python.org/downloads/)

Verifique a caixa Add Python à PATH.

![Python Windows Instala dialog Step 1](./media/pythoninstallone.jpg)

Adicione funcionalidades opcionais verificando as caixas e, em seguida, clique no botão 'Seguinte'.

![Python Windows Instala dialog Step 2](./media/pythoninstalltwo.jpg)

Escolha a "instalação personalizada" e desloque `C:\Python37-32\` o caminho de instalação como pasta de raiz, por exemplo, clique no botão 'Instalar'.

![Python Windows Instala dialog Step 3](./media/pythoninstallthree.jpg)

Depois da instalação Python estar `cd` completa, abra um Pedido de Comando e para a pasta de Scripts Python.

```cmd
cd C:\Python37-32\Scripts
```

Instale o Flask.

```cmd
pip install flask
```

Instale jinja2. Um motor de modelo completo para Python.

```cmd
pip install jinja2
```

Instale o virtualenv. Uma ferramenta para criar ambientes python isolados.

```cmd
pip install virtualenv
```

Instale o virtualenvwrapper-win. A ideia por trás do virtualenvwrapper é facilitar o uso do virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Instale o módulo de pedidos. Requests é uma biblioteca Apache2 Licensed HTTP, escrita em Python.

```cmd
pip install requests
```

Instale o módulo python-dotenv. Este módulo lê o par de valor-chave do ficheiro .env e adiciona-os à variável ambiental.

```cmd
pip install python-dotenv
```

Fazer um ambiente virtual

```cmd
mkvirtualenv advanced-python
```

`cd`para a pasta raiz do projeto de amostra.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Ligue o projeto da amostra com o ambiente. Isto mapeia o ambiente virtual recém-criado para a pasta raiz do projeto de amostra.

```cmd
setprojectdir .
```

Ativar o ambiente virtual.

```cmd
activate
```

O projeto deve agora estar ativo e `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` verá algo como no Comando.

Desativar o ambiente.

```cmd
deactivate
```

O `(advanced-python)` prefixo deve agora desaparecer à medida que o ambiente está agora desativado.

Para reativar `workon advanced-python` o ambiente executado a partir da pasta raiz do projeto da amostra.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Lançar o Leitor Imersivo com conteúdo de amostra

Quando o ambiente estiver ativo, execute `flask run` o projeto da amostra entrando na pasta raiz do projeto da amostra.

```cmd
flask run
```

Abra o seu _http://localhost:5000_navegador e navegue para .

## <a name="create-a-python-web-app-on-osx"></a>Crie uma aplicação web Python no OSX

Crie uma aplicação web Python utilizando `flask` no OSX.

Instale [Git](https://git-scm.com/).

Depois de git ser instalado terminal aberto e 'clone' o repositório Imersivo Do Leitor SDK Git para uma pasta no seu computador

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Instale [Python.](https://www.python.org/downloads/)

A pasta de raiz `Python37-32` Python, por exemplo, deve estar agora na pasta Aplicações.

Depois da instalação Python `cd` estar completa, abra o Terminal e a pasta de Scripts Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Instale o pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Em seguida, executar o seguinte para instalar pip para o utilizador atualmente assinado para evitar problemas de permissões.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Insira a sua palavra-passe, quando solicitado.
- Adicione o caminho da instalação do seu pip à sua variável PATH.
- Vá para a parte inferior do ficheiro e introduza o caminho que pretende adicionar `PATH=$PATH:/usr/local/bin`como o último item da lista, por exemplo.
- Bata no control-x para desistir.
- Introduza `Y` para salvar o tampão modificado.
- Já está! Para testá-lo, em nova `echo $PATH`janela terminal, escreva: .

Instale o Flask.

```bash
pip install flask --user
```

Instale jinja2. Um motor de modelo completo para Python.

```bash
pip install Jinja2 --user
```

Instale o virtualenv. Uma ferramenta para criar ambientes python isolados.

```bash
pip install virtualenv --user
```

Instale o virtualenvwrapper. A ideia por trás do virtualenvwrapper é facilitar o uso do virtualenv.

```bash
pip install virtualenvwrapper --user
```

Instale o módulo de pedidos. Requests é uma biblioteca Apache2 Licensed HTTP, escrita em Python.

```bash
pip install requests --user
```

Instale o módulo python-dotenv. Este módulo lê o par de valor-chave do ficheiro .env e adiciona-os à variável ambiental.

```bash
pip install python-dotenv --user
```

Escolha uma pasta onde gostaria de manter os seus ambientes virtuais e executar este comando

```bash
mkdir ~/.virtualenvs
```

`cd`para a pasta de aplicação de amostra sdk Python do leitor imersivo.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Fazer um ambiente virtual

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Ligue o projeto da amostra com o ambiente. Isto mapeia o ambiente virtual recém-criado para a pasta raiz do projeto de amostra.

```bash
setprojectdir .
```

Ativar o ambiente virtual.

```bash
activate
```

O projeto deve agora estar ativo e `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` verá algo como no Comando.

Desativar o ambiente.

```bash
deactivate
```

O `(advanced-python)` prefixo deve agora desaparecer à medida que o ambiente está agora desativado.

Para reativar `workon advanced-python` o ambiente executado a partir da pasta raiz do projeto da amostra.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Lançar o Leitor Imersivo com conteúdo de amostra

Quando o ambiente estiver ativo, execute `flask run` o projeto da amostra entrando na pasta raiz do projeto da amostra.

```bash
flask run
```

Abra o seu _http://localhost:5000_navegador e navegue para .

## <a name="next-steps"></a>Passos seguintes

* Explore o [SDK imersivo do leitor](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](./reference.md)
* Ver amostras de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
