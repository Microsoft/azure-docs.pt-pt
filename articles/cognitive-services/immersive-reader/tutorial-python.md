---
title: 'Tutorial: iniciar o leitor de imersão usando Python'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo Python que inicia o leitor de imersão.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.openlocfilehash: a252afae0a007ee0b791b56d19ffb0685848d30a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844365"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Tutorial: iniciar o leitor de imersão usando o projeto de exemplo do Python

Na [visão geral](./overview.md), você aprendeu sobre o que é o leitor de imersão e como ele implementa técnicas comprovadas para melhorar a compreensão da leitura para aprendizes de idioma, leitores emergentes e estudantes com diferenças de aprendizado. Este tutorial aborda como criar um aplicativo Web Python que inicia o leitor de imersão. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um aplicativo Web Python com Pip, Flask, jinja e virtualenv usando um projeto de exemplo
> * Adquirir um token de acesso
> * Iniciar o leitor de imersão com conteúdo de exemplo

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso de leitor de imersão configurado para Azure Active Directory autenticação. Siga [estas instruções](./how-to-create-immersive-reader.md) para configurar. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do ambiente. Salve a saída da sessão em um arquivo de texto para referência futura.
* [Git](https://git-scm.com/)
* [SDK do leitor de imersão](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) e [Pip](https://docs.python.org/3/installing/index.html). A partir do Python 3,4, o Pip é incluído por padrão com os instaladores de binários do Python.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) e [virtualenvwrapper-Win para Windows](https://pypi.org/project/virtualenvwrapper-win/) ou [virtualenvwrapper para OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [módulo de solicitações](https://pypi.org/project/requests/2.7.0/)
* Um IDE, como [Visual Studio Code](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>Configurar credenciais de autenticação

Crie um novo arquivo chamado _. env_e cole o código a seguir nele, fornecendo os valores fornecidos quando você criou o recurso de leitura de imersão.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Certifique-se de não confirmar esse arquivo no controle do código-fonte, pois ele contém segredos que não devem ser tornados públicos.

O ponto de extremidade da API do **getimmersivereadertoken** deve ser protegido por parte de alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que usuários não autorizados obtenham tokens para uso no seu serviço de leitor de imersão e cobrança; Esse trabalho está além do escopo deste tutorial.

## <a name="create-a-python-web-app-on-windows"></a>Criar um aplicativo Web Python no Windows

Crie um aplicativo Web Python usando o `flask` no Windows.

Instale o [Git](https://git-scm.com/).

Depois que o Git for instalado, abra um prompt de comando e ' clone ' o repositório Git do SDK do leitor de imersão para uma pasta no computador

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Instale o [Python](https://www.python.org/downloads/).

Marque a caixa Adicionar Python ao caminho.

![Caixa de diálogo de instalação do Python Windows etapa 1](./media/pythoninstallone.jpg)

Adicione recursos opcionais marcando as caixas e, em seguida, clique no botão ' Avançar '.

![Caixa de diálogo de instalação do Python Windows etapa 2](./media/pythoninstalltwo.jpg)

Escolha ' instalação personalizada ' e defina o caminho de instalação como sua pasta raiz, por exemplo, `C:\Python37-32\` clique no botão ' instalar '.

![Caixa de diálogo de instalação do Python Windows etapa 3](./media/pythoninstallthree.jpg)

Após a conclusão da instalação do Python, abra um prompt de comando e `cd` na pasta scripts do Python.

```cmd
cd C:\Python37-32\Scripts
```

Instale o Flask.

```cmd
pip install flask
```

Instale o Jinja2. Um mecanismo completo de modelo em destaque para Python.

```cmd
pip install jinja2
```

Instale o virtualenv. Uma ferramenta para criar ambientes de Python isolados.

```cmd
pip install virtualenv
```

Instale o virtualenvwrapper-Win. A ideia por trás do virtualenvwrapper é facilitar o uso de virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Instale o módulo solicitações. Solicitações é uma biblioteca HTTP licenciada do apache2, escrita em Python.

```cmd
pip install requests
```

Instale o módulo python-dotenv. Esse módulo lê o par chave-valor do arquivo. env e os adiciona à variável de ambiente.

```cmd
pip install python-dotenv
```

Criar um ambiente virtual

```cmd
mkvirtualenv advanced-python
```

`cd` à pasta raiz do projeto de exemplo.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Conecte o projeto de exemplo com o ambiente. Isso mapeia o ambiente virtual criado recentemente para a pasta raiz do projeto de exemplo.

```cmd
setprojectdir .
```

Ative o ambiente virtual.

```cmd
activate
```

O projeto agora deve estar ativo e você verá algo como `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` no prompt de comando.

Desative o ambiente.

```cmd
deactivate
```

Agora, o prefixo de `(advanced-python)` deve ser removido, pois o ambiente agora está desativado.

Para reativar o ambiente, execute `workon advanced-python` na pasta raiz do projeto de exemplo.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Iniciar o leitor de imersão com conteúdo de exemplo

Quando o ambiente estiver ativo, execute o projeto de exemplo inserindo `flask run` na pasta raiz do projeto de exemplo.

```cmd
flask run
```

Abra o navegador e navegue até _http://localhost:5000_ .

## <a name="create-a-python-web-app-on-osx"></a>Criar um aplicativo Web Python no OSX

Crie um aplicativo Web Python usando `flask` no OSX.

Instale o [Git](https://git-scm.com/).

Depois que o Git for instalado, abra o terminal e ' clone ' o repositório Git do SDK do leitor de imersão em uma pasta no computador

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Instale o [Python](https://www.python.org/downloads/).

A pasta raiz do Python, por exemplo, `Python37-32` deve estar agora na pasta aplicativos.

Após a conclusão da instalação do Python, abra o terminal e `cd` para a pasta scripts Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Instale o pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Em seguida, execute o seguinte para instalar o Pip para o usuário conectado no momento para evitar problemas de permissões.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Insira sua senha, quando solicitado.
- Adicione o caminho da instalação do Pip à variável PATH.
- Vá para a parte inferior do arquivo e insira o caminho que você deseja adicionar como o último item da lista, por exemplo, `PATH=$PATH:/usr/local/bin`.
- Pressione Control-x para sair.
- Insira `Y` para salvar o buffer modificado.
- Já está! Para testá-lo, na janela novo terminal, digite: `echo $PATH`.

Instale o Flask.

```bash
pip install flask --user
```

Instale o Jinja2. Um mecanismo completo de modelo em destaque para Python.

```bash
pip install Jinja2 --user
```

Instale o virtualenv. Uma ferramenta para criar ambientes de Python isolados.

```bash
pip install virtualenv --user
```

Instale o virtualenvwrapper. A ideia por trás do virtualenvwrapper é facilitar o uso de virtualenv.

```bash
pip install virtualenvwrapper --user
```

Instale o módulo solicitações. Solicitações é uma biblioteca HTTP licenciada do apache2, escrita em Python.

```bash
pip install requests --user
```

Instale o módulo python-dotenv. Esse módulo lê o par chave-valor do arquivo. env e os adiciona à variável de ambiente.

```bash
pip install python-dotenv --user
```

Escolha uma pasta na qual você gostaria de manter seus ambientes virtuais e execute este comando

```bash
mkdir ~/.virtualenvs
```

`cd` para a pasta de aplicativo de exemplo do SDK do leitor de imersão do Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Criar um ambiente virtual

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Conecte o projeto de exemplo com o ambiente. Isso mapeia o ambiente virtual criado recentemente para a pasta raiz do projeto de exemplo.

```bash
setprojectdir .
```

Ative o ambiente virtual.

```bash
activate
```

O projeto agora deve estar ativo e você verá algo como `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` no prompt de comando.

Desative o ambiente.

```bash
deactivate
```

Agora, o prefixo de `(advanced-python)` deve ser removido, pois o ambiente agora está desativado.

Para reativar o ambiente, execute `workon advanced-python` na pasta raiz do projeto de exemplo.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Iniciar o leitor de imersão com conteúdo de exemplo

Quando o ambiente estiver ativo, execute o projeto de exemplo inserindo `flask run` na pasta raiz do projeto de exemplo.

```bash
flask run
```

Abra o navegador e navegue até _http://localhost:5000_ .

## <a name="next-steps"></a>Passos seguintes

* Explore o [SDK do leitor de imersão](https://github.com/microsoft/immersive-reader-sdk) e a [referência do SDK do leitor de imersão](./reference.md)
* Exibir exemplos de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
