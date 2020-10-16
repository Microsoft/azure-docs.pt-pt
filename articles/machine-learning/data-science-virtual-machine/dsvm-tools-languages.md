---
title: Linguagens suportadas
titleSuffix: Azure Data Science Virtual Machine
description: As linguagens de programa suportadas e ferramentas relacionadas pré-instaladas na Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8a73336ba0a324c65ee0de764d81a8e69ab6079c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87843271"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Línguas suportadas na Máquina Virtual da Ciência de Dados 

A Máquina Virtual de Ciência de Dados (DSVM) vem com várias línguas pré-construídas e ferramentas de desenvolvimento para a construção das suas aplicações de inteligência artificial (IA). Aqui estão alguns dos notáveis.

## <a name="python-windows-server-2016-edition"></a>Python (edição windows Server 2016)

| Categoria | Valor |
| ------------- | ------------- |
| Versões linguísticas suportadas | Python 2.7 e 3.7 |
| Edições DSVM suportadas      | Windows Server 2016     |
| Como é configurado / instalado no DSVM?  | Dois `conda` ambientes globais são criados: <br /> * O `root` ambiente localizado é Python `/anaconda/` 3.7. <br/> * O `python2` ambiente localizado é Python `/anaconda/envs/python2` 2.7.       |
| Ligações a amostras      | Os cadernos da Amostra Jupyter para Python estão incluídos.     |
| Ferramentas relacionadas no DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> As construções do Windows Server 2016 que foram criadas antes de março de 2018 contêm Python 3.5 e Python 2.7. Python 2.7 é o ambiente **de raiz** conda, e **py37** é o ambiente Python 3.7.

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

* Corra com um pedido de comando:

  Abra uma pronta de comando e use um dos seguintes métodos, dependendo da versão de Python que pretende executar:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Utilização num IDE:

  Utilize ferramentas Python para Estúdio Visual (PTVS), instalado na edição da Comunidade visual Studio. Por padrão, o único ambiente que é configurado automaticamente em PTVS é Python 3.6. 

    > [!NOTE]
    > Para apontar PTVS no Python 2.7, você deve criar um ambiente personalizado em PTVS. Para definir este caminho ambiental na Edição Comunitária do Estúdio Visual, vá às **Ferramentas**  ->  **Python Python**  ->  **Ambientes** e selecione + **Personalizado.** Em seguida, desacione a localização para **c:\anaconda\envs\python2** e selecione **Auto Detect**.

* Uso em Jupyter:

  Abra o Jupyter e selecione **New** para criar um novo caderno. Você pode definir o tipo de núcleo como _Python [Conda Root]_ para Python 3.7 e _Python [Conda env:python2]_ para Python 2.7.

* Instalar pacotes Python:

  Os ambientes python predefinidos no DSVM são ambientes globais que são legíveis por todos os utilizadores. Mas só os administradores podem escrever e instalar pacotes globais. Para instalar pacotes no ambiente global, ative para o ambiente raiz ou python2 utilizando o `activate` comando como administrador. Em seguida, pode utilizar um gestor de pacotes como `conda` ou para instalar ou atualizar `pip` pacotes.

## <a name="python-linux-edition"></a>Python (edição Linux)

| Categoria | Valor |
| ------------- | ------------- |
| Versões linguísticas suportadas | Python 2.7 e 3.5 |
| Edições DSVM suportadas      | Linux   |
| Como é configurado / instalado no DSVM?  | Dois `conda` ambientes globais são criados: <br /> * `root` ambiente localizado em `/anaconda/` é Python 2.7. <br/> * `py35` ambiente localizado em `/anaconda/envs/py35` é Python 3.5.       |
| Ligações a amostras      | Os cadernos da Amostra Jupyter para Python estão incluídos.     |
| Ferramentas relacionadas no DSVM      | PySpark, R, Julia      |

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

* Correr num terminal:

  Abra o terminal e faça um dos seguintes, dependendo da versão de Python que pretende executar:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Utilização num IDE:

  Use o PyCharm, instalado na edição da Comunidade visual studio. 

* Uso em Jupyter:

  Abra o Jupyter e selecione **New** para criar um novo caderno. Você pode definir o tipo de núcleo como **Python [Conda Root]** para Python 2.7 e **Python [Conda env:py35]** para o ambiente Python 3.5. 

* Instalar pacotes Python:

  Os ambientes python predefinidos no DSVM são ambientes globais legíveis por todos os utilizadores. Mas só os administradores podem escrever e instalar pacotes globais. Para instalar pacotes no ambiente global, ative para o ambiente raiz ou py35 utilizando o `source activate` comando como administrador ou como utilizador com permissões de sudo. Em seguida, pode utilizar um gestor de pacotes como `conda` ou para instalar ou atualizar `pip` pacotes.


## <a name="r"></a>R

| Categoria | Valor |
| ------------- | ------------- |
| Versões linguísticas suportadas | Microsoft R Open 3.x (100% compatível com CRAN-R)<br /> Microsoft R Server 9.x Developer edition (uma plataforma R pronta para empresas escaláveis)|
| Edições DSVM suportadas      | Linux, Windows     |
| Como é configurado / instalado no DSVM?  | Janelas: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Ligações a amostras      | Os cadernos da Amostra Jupyter para R estão incluídos.     |
| Ferramentas relacionadas no DSVM      | SparkR, Python, Julia      |

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

**Windows**:

* Corra com um pedido de comando:

  Abra uma janela da linha de comandos e escreva `R`.

* Utilização num IDE:

  Utilize RTools para Visual Studio (RTVS) instalados na edição da Comunidade visual studio ou RStudio. Estes estão disponíveis no menu Iniciar ou como um ícone de ambiente de trabalho. 

* Uso em Jupyter

  Abra o Jupyter e selecione **New** para criar um novo caderno. Pode definir o tipo de núcleo como **R** para utilizar o núcleo Jupyter R (IRKernel).

* Instalar pacotes R:

  R é instalado no DSVM num ambiente global que é legível por todos os utilizadores. Mas só os administradores podem escrever e instalar pacotes globais. Para instalar pacotes para o ambiente global, executar R utilizando um dos métodos anteriores. Em seguida, pode executar o gestor de pacotes R `install.packages()` para instalar ou atualizar pacotes.

**Linux:**

* Correr no terminal:

  Abra um terminal e `R` corra.  

* Utilização num IDE:

  Utilizar RStudio, instalado no Linux DSVM.  

* Uso em Jupyter:

  Abra o Jupyter e selecione **New** para criar um novo caderno. Pode definir o tipo de núcleo como **R** para utilizar o núcleo Jupyter R (IRKernel). 

* Instalar pacotes R:

  R é instalado no DSVM num ambiente global que é legível por todos os utilizadores. Mas só os administradores podem escrever e instalar pacotes globais. Para instalar pacotes para o ambiente global, executar R utilizando um dos métodos anteriores. Em seguida, pode executar o gestor de pacotes R `install.packages()` para instalar ou atualizar pacotes.


## <a name="julia"></a>Julia

| Categoria | Valor |
| ------------- | ------------- |
| Versões linguísticas suportadas | 0.6 |
| Edições DSVM suportadas      | Linux, Windows     |
| Como é configurado / instalado no DSVM?  | Janelas: Instalado em `C:\JuliaPro-VERSION`<br /> Linux: Instalado em `/opt/JuliaPro-VERSION`    |
| Ligações a amostras      | Os cadernos do Jupyter para a Julia estão incluídos.     |
| Ferramentas relacionadas no DSVM      | Python      |

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

**Windows**:

* Corra em um pedido de comando

  Abra um pedido de comando e corra `julia` .
* Utilização num IDE:

  Utilize `Juno` com o Julia IDE instalado no DSVM e disponível como atalho para desktop.

* Uso em Jupyter:

  Abra o Jupyter e selecione **New** para criar um novo caderno. Pode definir o tipo de núcleo como **Versão Julia**.

* Instalar pacotes Julia:

  A localização padrão da Julia é um ambiente global que é legível por todos os utilizadores. Mas só os administradores podem escrever e instalar pacotes globais. Para instalar pacotes para o ambiente global, gere a Julia utilizando um dos métodos anteriores. Em seguida, pode executar comandos de gestor de pacotes Julia como `Pkg.add()` instalar ou atualizar pacotes.


**Linux:**
* Correr num terminal:

  Abra um terminal e `julia` corra.
* Utilização num IDE:

  Utilização `Juno` , com o IDE Julia instalado no DSVM e disponível como atalho do menu **Aplicação.**

* Uso em Jupyter:

  Abra o Jupyter e selecione **New** para criar um novo caderno. Pode definir o tipo de núcleo como **Versão Julia**.

* Instalar pacotes Julia:

  A localização padrão da Julia é um ambiente global que é legível por todos os utilizadores. Mas só os administradores podem escrever e instalar pacotes globais. Para instalar pacotes para o ambiente global, gere a Julia utilizando um dos métodos anteriores. Em seguida, pode executar comandos de gestor de pacotes Julia como `Pkg.add()` instalar ou atualizar pacotes.

## <a name="other-languages"></a>Outras linguagens

**C:** Disponível no Windows e acessível através da edição da Comunidade Visual Studio ou no `Developer Command Prompt for Visual Studio` , onde pode executar o `csc` comando.

**Java**: OpenJDK está disponível nas edições Linux e Windows do DSVM e está definido no caminho. Para utilizar Java, digite o `javac` ou `java` comande num pedido de comando no Windows ou na casca de bata em Linux.

**Node.js**: Node.js está disponível nas edições Linux e Windows do DSVM e está definida no caminho. Para aceder Node.js, digite o `node` ou o comando num pedido de comando no Windows ou na casca de choque em `npm` Linux. No Windows, a extensão do Estúdio Visual para as ferramentas Node.js é instalada para fornecer um IDE gráfico para desenvolver a sua aplicação Node.js.

**F:** Disponível no Windows e acessível através da edição da Comunidade Visual Studio ou num `Developer Command Prompt for Visual Studio` , onde pode executar o `fsc` comando.
