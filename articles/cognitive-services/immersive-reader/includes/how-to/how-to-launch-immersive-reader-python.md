---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: f88ca39a378e997bb72300188166192e3383f6f1
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620183"
---
## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Um recurso imersivo do Leitor configurado para a autenticação do Azure Ative Directory. Siga [estas instruções](../../how-to-create-immersive-reader.md) para se preparar.  Você precisará de alguns dos valores criados aqui ao configurar as propriedades ambientais. Guarde a saída da sua sessão num ficheiro de texto para referência futura.
* [Git.](https://git-scm.com/)
* [Leitor Imersivo SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Python](https://www.python.org/downloads/) e [pip.](https://docs.python.org/3/installing/index.html) Começando pelo Python 3.4, o pip é incluído por defeito com os instaladores binários Python.
* [Frasco.](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja.](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) e [virtualenvwrapper-win para Windows](https://pypi.org/project/virtualenvwrapper-win/) ou [virtualenvwrapper para OSX](https://virtualenvwrapper.readthedocs.io/en/latest/).
* O [módulo de pedidos.](https://pypi.org/project/requests/2.7.0/)
* Um IDE como [o Código do Estúdio Visual.](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>Configure credenciais de autenticação

Crie um novo ficheiro chamado **.env** e cole-o os seguintes nomes e valores. Forneça os valores dados quando criou o seu recurso Leitor Imersivo.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Não comprometa este ficheiro em controlo de fontes porque contém segredos que não devem ser tornados públicos.

Fixe o ponto final **getimmersivereadertoken** API por trás de alguma forma de autenticação, por exemplo, [OAuth](https://oauth.net/2/). A autenticação impede que utilizadores não autorizados obtenham fichas para usar contra o seu serviço de Leitor imersivo e faturação. Este trabalho está fora do âmbito deste tutorial.

## <a name="create-a-python-web-app-on-windows"></a>Crie uma aplicação web Python no Windows

Crie uma aplicação web Python utilizando `flask` no Windows.

Instale [git](https://git-scm.com/).

Depois de o Git ser instalado, abra um pedido de comando e clone o repositório Imersivo Reader SDK Git para uma pasta no seu computador.

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Instale [python](https://www.python.org/downloads/).

Selecione a caixa de verificação **Add Python para PATH.**

![Pitões Windows Instale o passo de diálogo 1](../../media/pythoninstallone.jpg)

Adicione **funcionalidades opcionais** selecionando caixas de verificação e, em seguida, selecione **Seguinte**.

![Pitões Windows Instale o passo de diálogo 2](../../media/pythoninstalltwo.jpg)

Selecione **a instalação Personalizada**, e desacorra o caminho de instalação como a sua pasta de raiz, por exemplo, `C:\Python37-32\` . Em seguida, **selecione Instalar**.

![Pitões Windows Instale o passo de diálogo 3](../../media/pythoninstallthree.jpg)

Depois de terminada a instalação Python, abra um pedido de comando e use `cd` para ir à pasta Python Scripts.

```cmd
cd C:\Python37-32\Scripts
```

Instale o Balão.

```cmd
pip install flask
```

Instale o Jinja2. É um motor de modelo completo para Python.

```cmd
pip install jinja2
```

Instale virtualenv. Esta ferramenta cria ambientes Python isolados.

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

Instale o módulo python-dotenv. Este módulo lê o par de valores-chave a partir do ficheiro .env e adiciona-os à variável ambiente.

```cmd
pip install python-dotenv
```

Faça um ambiente virtual.

```cmd
mkvirtualenv advanced-python
```

Utilize `cd` para ir à pasta raiz do projeto de amostra.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Ligue o projeto da amostra ao ambiente. Esta ação mapeia o ambiente virtual recém-criado para a pasta raiz do projeto de amostra.

```cmd
setprojectdir .
```

Ativar o ambiente virtual.

```cmd
activate
```

O projeto deve agora estar ativo, e verá algo como `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` no pedido de comando.

Desativar o ambiente.

```cmd
deactivate
```

O `(advanced-python)` prefixo deve desaparecer porque o ambiente está desativado.

Para reativar o ambiente, corra `workon advanced-python` a partir da pasta raiz do projeto de amostra.

```cmd
workon advanced-python
```

### <a name="start-the-immersive-reader-with-sample-content"></a>Inicie o Leitor Imersivo com o conteúdo da amostra

Quando o ambiente estiver ativo, executar o projeto de amostra entrando `flask run` na pasta raiz do projeto de amostra.

```cmd
flask run
```

Abra o seu navegador e vá para http://localhost:5000 .

## <a name="create-a-python-web-app-on-osx"></a>Crie uma aplicação web Python no OSX

Crie uma aplicação web Python utilizando `flask` o OSX.

Instale [git](https://git-scm.com/).

Depois de o Git ser instalado, abra o Terminal e clone o repositório Imersivo Reader SDK Git para uma pasta no seu computador.

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Instale [python](https://www.python.org/downloads/).

A pasta raiz Python, por exemplo, `Python37-32` deve estar agora na pasta Aplicações.

Depois de terminada a instalação Python, abra o Terminal e `cd` use-o para ir à pasta Python Scripts.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Instale o pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Executar o seguinte código para instalar pip para o utilizador atualmente inscrito para evitar problemas de permissões.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Insira a sua palavra-passe, quando solicitado.
- Adicione o caminho da sua instalação do pip à variável PATH.
- Vá para a parte inferior do ficheiro e insira o caminho que pretende adicionar como o último item da lista, por exemplo, `PATH=$PATH:/usr/local/bin` .
- Selecione **CTRL+X** para desistir.
- Introduza **Y** para salvar o tampão modificado.
- Já está! Para testá-lo, numa nova janela do Terminal, `echo $PATH` entre.

Instale o Balão.

```bash
pip install flask --user
```

Instale o Jinja2. É um motor de modelo completo para Python.

```bash
pip install Jinja2 --user
```

Instale virtualenv. Esta ferramenta cria ambientes Python isolados.

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

Instale o módulo python-dotenv. Este módulo lê o par de valores-chave a partir do ficheiro .env e adiciona-os à variável ambiente.

```bash
pip install python-dotenv --user
```

Escolha uma pasta onde pretende manter os seus ambientes virtuais e execute este comando:

```bash
mkdir ~/.virtualenvs
```

Utilize `cd` para ir à pasta de aplicação da amostra SDK Python do leitor imersivo.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Faça um ambiente virtual.

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Ligue o projeto da amostra ao ambiente. Esta ação mapeia o ambiente virtual recém-criado para a pasta raiz do projeto de amostra.

```bash
setprojectdir .
```

Ativar o ambiente virtual.

```bash
activate
```

O projeto deve agora estar ativo, e verá algo como `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` no pedido de comando.

Desativar o ambiente.

```bash
deactivate
```

O `(advanced-python)` prefixo deve desaparecer porque o ambiente está desativado.

Para reativar o ambiente, corra `workon advanced-python` a partir da pasta raiz do projeto de amostra.

```bash
workon advanced-python
```

## <a name="start-the-immersive-reader-with-sample-content"></a>Inicie o Leitor Imersivo com o conteúdo da amostra

Quando o ambiente estiver ativo, executar o projeto de amostra entrando `flask run` na pasta raiz do projeto de amostra.

```bash
flask run
```

Abra o seu navegador e vá para http://localhost:5000 .

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência Imersiva SDK](https://github.com/microsoft/immersive-reader-sdk) do leitor e a [referência SDK do leitor imersivo](../../reference.md).
* Ver amostras de código no [GitHub.](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
