---
title: 'Tutorial: Lançar o Leitor Imersivo usando Python'
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
ms.custom: tracking-python
ms.openlocfilehash: fb95e21f9df5cf5eded03ea51f56bab86bbbecb3
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609136"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Tutorial: Lançar o Leitor Imersivo usando o projeto de amostra python

Na [visão geral,](./overview.md)aprendeu sobre o que é o Leitor Imersivo e como implementa técnicas comprovadas para melhorar a compreensão da leitura para os alunos de línguas, leitores emergentes e alunos com diferenças de aprendizagem. Este tutorial abrange como criar uma aplicação web Python que lança o Leitor Imersivo. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Crie uma aplicação web Python com Pip, Flask, Jinja e virtualenv usando um projeto de amostra
> * Adquirir um token de acesso
> * Lançar o Leitor Imersivo com conteúdo de amostra

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso imersivo do Leitor configurado para a autenticação do Azure Ative Directory. Siga [estas instruções](./how-to-create-immersive-reader.md) para se preparar. Você precisará de alguns dos valores criados aqui ao configurar as propriedades ambientais. Guarde a saída da sua sessão num ficheiro de texto para referência futura.
* [Git](https://git-scm.com/)
* [SDK leitor imersivo](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) e [pip.](https://docs.python.org/3/installing/index.html) Começando pelo Python 3.4, o pip é incluído por defeito com os instaladores binários Python.
* [Balão](https://flask.palletsprojects.com/en/1.0.x/)
* [Rio Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) e [virtualenvwrapper-win para Windows](https://pypi.org/project/virtualenvwrapper-win/) ou [virtualenvwrapper para OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [módulo de pedidos](https://pypi.org/project/requests/2.7.0/)
* Um IDE como [Código de Estúdio Visual](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>Configure credenciais de autenticação

Crie um novo ficheiro chamado _.env_e cole-o o seguinte código, fornecendo os valores dados quando criou o seu recurso Leitor Imersivo.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Certifique-se de que não compromete este ficheiro em controlo de fontes, pois contém segredos que não devem ser tornados públicos.

O ponto final **getimmersivereadertoken** API deve ser protegido por trás de alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que utilizadores não autorizados obtenham fichas para usar contra o seu serviço de leitor imersivo e faturação; que o trabalho está fora do âmbito deste tutorial.

## <a name="create-a-python-web-app-on-windows"></a>Crie uma aplicação web Python no Windows

Crie uma aplicação web Python utilizando `flask` no Windows.

Instale [git](https://git-scm.com/).

Depois de o Git ser instalado, abra um Pedido de Comando e 'clone' o repositório Imersivo Reader SDK Git para uma pasta no seu computador

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Instale [python](https://www.python.org/downloads/).

Verifique a caixa Add Python para a caixa PATH.

![Janelas python instalar o passo 1](./media/pythoninstallone.jpg)

Adicione funcionalidades opcionais verificando as caixas e, em seguida, clique no botão 'Seguinte'.

![Janelas python instalar o passo 2](./media/pythoninstalltwo.jpg)

Escolha 'Instalação personalizada' e defina o caminho de instalação como pasta de raiz, por `C:\Python37-32\` exemplo, clique no botão 'Instalar'.

![Janelas python instalar o passo 3](./media/pythoninstallthree.jpg)

Depois da instalação Python estar concluída, abra um Pedido de Comando e `cd` a pasta Scripts Python.

```cmd
cd C:\Python37-32\Scripts
```

Instale o Balão.

```cmd
pip install flask
```

Instale o Jinja2. Um motor de modelo completo para Python.

```cmd
pip install jinja2
```

Instale virtualenv. Uma ferramenta para criar ambientes python isolados.

```cmd
pip install virtualenv
```

Instale virtualenvwrapper-win. A ideia por trás do virtualenvwrapper é facilitar o uso do virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Instale o módulo de pedidos. Os pedidos são uma biblioteca HTTP licenciada Apache2, escrita em Python.

```cmd
pip install requests
```

Instale o módulo python-dotenv. Este módulo lê o par de valor-chave a partir de ficheiro .env e adiciona-os à variável ambiental.

```cmd
pip install python-dotenv
```

Faça um ambiente virtual

```cmd
mkvirtualenv advanced-python
```

`cd`para a pasta raiz do projeto de amostra.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Ligue o projeto da amostra ao ambiente. Isto mapeia o ambiente virtual recém-criado para a pasta raiz do projeto de amostra.

```cmd
setprojectdir .
```

Ativar o ambiente virtual.

```cmd
activate
```

O projeto deve agora estar ativo e você verá algo como `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` no Pedido de Comando.

Desativar o ambiente.

```cmd
deactivate
```

O `(advanced-python)` prefixo deve agora desaparecer, uma vez que o ambiente está agora desativado.

Para reativar o ambiente é `workon advanced-python` executado a partir da pasta raiz do projeto de amostra.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Lançar o Leitor Imersivo com conteúdo de amostra

Quando o ambiente estiver ativo, executar o projeto de amostra entrando `flask run` na pasta raiz do projeto de amostra.

```cmd
flask run
```

Abra o seu navegador e navegue para _http://localhost:5000_ .

## <a name="create-a-python-web-app-on-osx"></a>Crie uma aplicação web Python no OSX

Crie uma aplicação web Python utilizando `flask` o OSX.

Instale [git](https://git-scm.com/).

Depois de o Git ser instalado, abra o Terminal e 'clone' o repositório Imersivo Reader SDK Git para uma pasta no seu computador

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Instale [python](https://www.python.org/downloads/).

A pasta raiz Python, por exemplo, `Python37-32` deve estar agora na pasta Aplicações.

Depois da instalação Python estar completa, abra o Terminal e `cd` a pasta Python Scripts.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Instale o pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Em seguida, executar o seguinte para instalar pip para o utilizador atualmente inscrito para evitar problemas de permissões.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Insira a sua palavra-passe, quando solicitado.
- Adicione o caminho da sua instalação do pip à variável PATH.
- Vá para a parte inferior do ficheiro e insira o caminho que pretende adicionar como o último item da lista, por `PATH=$PATH:/usr/local/bin` exemplo.
- Acerte o control-x para desistir.
- Introduza `Y` para salvar o tampão modificado.
- Já está. Para testá-lo, na nova janela do Terminal, escreva: `echo $PATH` .

Instale o Balão.

```bash
pip install flask --user
```

Instale o Jinja2. Um motor de modelo completo para Python.

```bash
pip install Jinja2 --user
```

Instale virtualenv. Uma ferramenta para criar ambientes python isolados.

```bash
pip install virtualenv --user
```

Instale virtualenvwrapper. A ideia por trás do virtualenvwrapper é facilitar o uso do virtualenv.

```bash
pip install virtualenvwrapper --user
```

Instale o módulo de pedidos. Os pedidos são uma biblioteca HTTP licenciada Apache2, escrita em Python.

```bash
pip install requests --user
```

Instale o módulo python-dotenv. Este módulo lê o par de valor-chave a partir de ficheiro .env e adiciona-os à variável ambiental.

```bash
pip install python-dotenv --user
```

Escolha uma pasta onde gostaria de manter os seus ambientes virtuais e executar este comando

```bash
mkdir ~/.virtualenvs
```

`cd`para a pasta de aplicação da amostra SDK Python do leitor imersivo.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Faça um ambiente virtual

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Ligue o projeto da amostra ao ambiente. Isto mapeia o ambiente virtual recém-criado para a pasta raiz do projeto de amostra.

```bash
setprojectdir .
```

Ativar o ambiente virtual.

```bash
activate
```

O projeto deve agora estar ativo e você verá algo como `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` no Pedido de Comando.

Desativar o ambiente.

```bash
deactivate
```

O `(advanced-python)` prefixo deve agora desaparecer, uma vez que o ambiente está agora desativado.

Para reativar o ambiente é `workon advanced-python` executado a partir da pasta raiz do projeto de amostra.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Lançar o Leitor Imersivo com conteúdo de amostra

Quando o ambiente estiver ativo, executar o projeto de amostra entrando `flask run` na pasta raiz do projeto de amostra.

```bash
flask run
```

Abra o seu navegador e navegue para _http://localhost:5000_ .

## <a name="next-steps"></a>Próximos passos

* Explore o [SDK do leitor imersivo](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](./reference.md)
* Ver amostras de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
