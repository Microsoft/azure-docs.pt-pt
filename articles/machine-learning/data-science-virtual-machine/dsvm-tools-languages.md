---
title: Linguagens suportadas
titleSuffix: Azure Data Science Virtual Machine
description: Os idiomas do programa suportados e ferramentas relacionadas pré-instaladas na Máquina Virtual da Ciência dos Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: e7b32579712e89c0d5595303ee7e03d8b2462607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283657"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Idiomas suportados na Máquina Virtual da Ciência dos Dados 

A Máquina Virtual de Ciência de Dados (DSVM) vem com várias linguagens pré-construídas e ferramentas de desenvolvimento para a construção das suas aplicações de inteligência artificial (IA). Aqui estão alguns dos notáveis.

## <a name="python-windows-server-2016-edition"></a>Python (edição do Windows Server 2016)

|    |           |
| ------------- | ------------- |
| Versões linguísticas suportadas | Python 2.7 e 3.7 |
| Edições DSVM suportadas      | Windows Server 2016     |
| Como é configurado /instalado no DSVM?  | São `conda` criados dois ambientes globais: <br /> * `root` O ambiente `/anaconda/` localizado é Python 3.7. <br/> * `python2` O ambiente `/anaconda/envs/python2` localizado é Python 2.7.       |
| Ligações a amostras      | Estão incluídos os cadernos Jupyter para Python.     |
| Ferramentas relacionadas no DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> As construções do Windows Server 2016 que foram criadas antes de março de 2018 contêm Python 3.5 e Python 2.7. Python 2.7 é o ambiente **de raiz** de conda, e **py37** é o ambiente Python 3.7.

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

* Corra a um pedido de comando:

  Abra um pedido de comando e use um dos seguintes métodos, dependendo da versão de Python que pretende executar:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Utilização num IDE:

  Utilize ferramentas Python para estúdio visual (PTVS), instaladas na edição visual do Estúdio Comunitário. Por padrão, o único ambiente que é configurado automaticamente no PTVS é python 3.6. 

    > [!NOTE]
    > Para apontar PTVS em Python 2.7, você deve criar um ambiente personalizado em PTVS. Para definir este caminho ambiental na Edição Comunitária do Estúdio Visual, vá a **Tools** -> **Python Tools** -> **Python Environments** e selecione **+ Custom**. Em seguida, defina a localização para **c:\anaconda\envs\python2** e selecione **Deteção automática**.

* Utilização em Jupyter:

  Abra o Jupyter e selecione **New** para criar um novo caderno. Você pode definir o tipo de kernel como _Python [Raiz de Conda]_ para Python 3.7 e _Python [Conda env:python2]_ para Python 2.7.

* Instale pacotes Python:

  Os ambientes padrão da Python no DSVM são ambientes globais que são legíveis por todos os utilizadores. Mas só os administradores podem escrever e instalar pacotes globais. Para instalar pacotes no ambiente global, ative-se no `activate` ambiente raiz ou python2 utilizando o comando como administrador. Em seguida, pode utilizar `conda` um `pip` gestor de pacotes como ou para instalar ou atualizar pacotes.

## <a name="python-linux-edition"></a>Python (edição Linux)

|    |           |
| ------------- | ------------- |
| Versões linguísticas suportadas | Python 2.7 e 3.5 |
| Edições DSVM suportadas      | Linux   |
| Como é configurado /instalado no DSVM?  | São `conda` criados dois ambientes globais: <br /> * `root`ambiente localizado `/anaconda/` em é Python 2.7. <br/> * `py35`ambiente localizado `/anaconda/envs/py35`em é Python 3.5.       |
| Ligações a amostras      | Estão incluídos os cadernos Jupyter para Python.     |
| Ferramentas relacionadas no DSVM      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

* Correr num terminal:

  Abra o terminal e faça um dos seguintes, dependendo da versão da Python que pretende executar:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Utilização num IDE:

  Use pyCharm, instalado na edição visual studio community. 

* Utilização em Jupyter:

  Abra o Jupyter e selecione **New** para criar um novo caderno. Você pode definir o tipo de kernel como **Python [Raiz de Conda]** para Python 2.7 e **Python [Conda env:py35]** para o ambiente Python 3.5. 

* Instale pacotes Python:

  Os ambientes padrão da Python no DSVM são ambientes globais legíveis por todos os utilizadores. Mas só os administradores podem escrever e instalar pacotes globais. Para instalar pacotes no ambiente global, ative-se no ambiente `source activate` raiz ou py35 utilizando o comando como administrador ou como utilizador com permissões sudo. Em seguida, pode utilizar `conda` um `pip` gestor de pacotes como ou para instalar ou atualizar pacotes.


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Versões linguísticas suportadas | Microsoft R Open 3.x (100% compatível com CRAN-R)<br /> Edição do Microsoft R Server 9.x Developer (uma plataforma R pronta para a empresa)|
| Edições DSVM suportadas      | Linux     |
| Como é configurado /instalado no DSVM?  | Janelas:`C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux:`/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Ligações a amostras      | Estão incluídos os cadernos Jupyter para R.     |
| Ferramentas relacionadas no DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

**Janelas:**

* Corra a um pedido de comando:

  Abra uma janela da linha de comandos e escreva `R`.

* Utilização num IDE:

  Utilize RTools para Estúdio Visual (RTVS) instalados na edição visual do Estúdio Comunitário ou RStudio. Estes estão disponíveis no menu Iniciar ou como um ícone de ambiente de trabalho. 

* Uso em Jupyter

  Abra o Jupyter e selecione **New** para criar um novo caderno. Pode definir o tipo de núcleo como **R** para utilizar o núcleo Jupyter R (IRKernel).

* Instale pacotes R:

  R é instalado no DSVM em um ambiente global que é legível por todos os utilizadores. Mas só os administradores podem escrever e instalar pacotes globais. Para instalar pacotes no ambiente global, executar R utilizando um dos métodos anteriores. Em seguida, pode executar `install.packages()` o gestor de pacotes R para instalar ou atualizar pacotes.

**Linux:**

* Correr em terminal:

  Abra um terminal `R`e corra.  

* Utilização num IDE:

  Utilize o RStudio, instalado no Linux DSVM.  

* Utilização em Jupyter:

  Abra o Jupyter e selecione **New** para criar um novo caderno. Pode definir o tipo de núcleo como **R** para utilizar o núcleo Jupyter R (IRKernel). 

* Instale pacotes R:

  R é instalado no DSVM em um ambiente global que é legível por todos os utilizadores. Mas só os administradores podem escrever e instalar pacotes globais. Para instalar pacotes no ambiente global, executar R utilizando um dos métodos anteriores. Em seguida, pode executar `install.packages()` o gestor de pacotes R para instalar ou atualizar pacotes.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Versões linguísticas suportadas | 0.6 |
| Edições DSVM suportadas      | Linux     |
| Como é configurado /instalado no DSVM?  | Windows: Instalado em`C:\JuliaPro-VERSION`<br /> Linux: Instalado em`/opt/JuliaPro-VERSION`    |
| Ligações a amostras      | Amostra de cadernos Jupyter para Julia estão incluídos.     |
| Ferramentas relacionadas no DSVM      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

**Janelas:**

* Correr a uma solicitação de comando

  Abra um pedido `julia`de comando e corra.
* Utilização num IDE:

  Utilize `Juno` com a Julia IDE instalada no DSVM e disponível como atalho para desktop.

* Utilização em Jupyter:

  Abra o Jupyter e selecione **New** para criar um novo caderno. Pode definir o tipo de kernel como **versão julia**.

* Instale pacotes Julia:

  A localização padrão da Julia é um ambiente global que é legível por todos os utilizadores. Mas só os administradores podem escrever e instalar pacotes globais. Para instalar pacotes no ambiente global, gerea Julia utilizando um dos métodos anteriores. Depois, pode executar comandos do `Pkg.add()` gestor de pacotes julia como instalar ou atualizar pacotes.


**Linux:**
* Correr num terminal:

  Abra um terminal `julia`e corra.
* Utilização num IDE:

  Utilizar, `Juno`com a Julia IDE instalada no DSVM e disponível como atalho do menu **aplicação.**

* Utilização em Jupyter:

  Abra o Jupyter e selecione **New** para criar um novo caderno. Pode definir o tipo de kernel como **versão julia**.

* Instale pacotes Julia:

  A localização padrão da Julia é um ambiente global que é legível por todos os utilizadores. Mas só os administradores podem escrever e instalar pacotes globais. Para instalar pacotes no ambiente global, gerea Julia utilizando um dos métodos anteriores. Depois, pode executar comandos do `Pkg.add()` gestor de pacotes julia como instalar ou atualizar pacotes.

## <a name="other-languages"></a>Outras línguas

**C#**: Disponível no Windows e acessível `Developer Command Prompt for Visual Studio`através da edição `csc` visual do Estúdio Comunitário ou na , onde pode executar o comando.

**Java**: OpenJDK está disponível nas edições Linux e Windows do DSVM e está definido no caminho. Para utilizar Java, `javac` `java` escreva o ou comando num pedido de comando no Windows ou na concha de festa em Linux.

**Node.js**: Node.js está disponível nas edições Linux e Windows do DSVM e está definido no caminho. Para aceder ao Node.js, escreva o `node` ou `npm` comando num pedido de comando no Windows ou na concha de festa em Linux. No Windows, a extensão do Estúdio Visual para as ferramentas Node.js está instalada para fornecer um IDE gráfico para desenvolver a sua aplicação Node.js.

**F#**: Disponível no Windows e acessível `Developer Command Prompt for Visual Studio`através da edição `fsc` visual do Estúdio Comunitário ou numa , onde pode executar o comando.
