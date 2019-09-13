---
title: Idiomas suportados
titleSuffix: Azure Data Science Virtual Machine
description: Os idiomas do programa com suporte e as ferramentas relacionadas pré-instaladas no Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 0cedc9ede43d18d0b94b8a516170db53e3a27910
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885625"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Idiomas suportados na máquina de Virtual de ciência de dados 

O Máquina Virtual de Ciência de Dados (DSVM) vem com várias linguagens predefinidas e ferramentas de desenvolvimento para criar seus aplicativos de ia (inteligência artificial). Aqui estão algumas das notáveis.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 Edition)

|    |           |
| ------------- | ------------- |
| Versões de idioma com suporte | Python 2,7 e 3,6 |
| Edições DSVM com suporte      | Windows Server 2016     |
| Como é ele configurado / instalado no DSVM?  | Dois ambientes `conda` globais são criados: <br /> * O `root` ambiente localizado em `/anaconda/` é Python 3,6. <br/> * O `python2` ambiente localizado em `/anaconda/envs/python2` é Python 2,7.       |
| Links para amostras      | Os notebooks Jupyter de exemplo para Python estão incluídos.     |
| Ferramentas relacionadas no DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> As compilações do Windows Server 2016 que foram criadas antes de 2018 de março contêm Python 3,5 e Python 2,7. O Python 2,7 é o ambiente **raiz** Conda e **py35** é o ambiente Python 3,5.

### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

* Executar em um prompt de comando:

  Abra um prompt de comando e use um dos seguintes métodos, dependendo da versão do Python que você deseja executar:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.6
    activate 
    python --version 
    ```
    
* Use em um IDE:

  Use o Ferramentas Python para Visual Studio (PTVS), instalado no Visual Studio Community Edition. Por padrão, o único ambiente configurado automaticamente no PTVS é Python 3,6. 

    > [!NOTE]
    > Para apontar o PTVS no Python 2,7, você deve criar um ambiente personalizado no PTVS. Para definir esse caminho de ambiente no Visual Studio Community Edition, vá para **ferramentas** -> **Python** -> **ambientes Python** e selecione **+ personalizado**. Em seguida, defina o local como **c:\anaconda\envs\python2** e selecione **detecção automática**.

* Use em Jupyter:

  Abra Jupyter e selecione **novo** para criar um novo bloco de anotações. Você pode definir o tipo de kernel como _Python [Conda root]_ para Python 3,6 e _Python [Conda env: Python2]_ para Python 2,7.

* Instalar pacotes do Python:

  Os ambientes padrão do Python no DSVM são ambientes globais que podem ser lidos por todos os usuários. Mas somente os administradores podem gravar e instalar pacotes globais. Para instalar pacotes no ambiente global, ative para o ambiente raiz ou python2 usando o `activate` comando como administrador. Em seguida, você pode usar um Gerenciador de `conda` pacotes `pip` como ou para instalar ou atualizar pacotes.

## <a name="python-linux-and-windows-server-2012-edition"></a>Python (Linux e Windows Server 2012 Edition)

|    |           |
| ------------- | ------------- |
| Versões de idioma com suporte | Python 2,7 e 3,5 |
| Edições DSVM com suporte      | Linux, Windows Server 2012    |
| Como é ele configurado / instalado no DSVM?  | Dois ambientes `conda` globais são criados: <br /> * `root`o ambiente localizado `/anaconda/` em é Python 2,7. <br/> * `py35`o ambiente localizado `/anaconda/envs/py35`em é Python 3,5.       |
| Links para amostras      | Os notebooks Jupyter de exemplo para Python estão incluídos.     |
| Ferramentas relacionadas no DSVM      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

**Linux**
* Executar em um terminal:

  Abra o terminal e faça um dos seguintes, dependendo da versão do Python que você deseja executar:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Use em um IDE:

  Use o PyCharm, instalado no Visual Studio Community Edition. 

* Use em Jupyter:

  Abra Jupyter e selecione **novo** para criar um novo bloco de anotações. Você pode definir o tipo de kernel como **Python [Conda root]** para Python 2,7 e **Python [Conda env: py35]** para o ambiente Python 3,5. 

* Instalar pacotes do Python:

  Os ambientes de Python padrão na DSVM são ambientes global legíveis por todos os utilizadores. Mas somente os administradores podem gravar e instalar pacotes globais. Para instalar pacotes no ambiente global, ative para o ambiente raiz ou py35 usando o `source activate` comando como administrador ou como um usuário com permissões sudo. Em seguida, você pode usar um Gerenciador de `conda` pacotes `pip` como ou para instalar ou atualizar pacotes.

**Windows 2012**
* Executar em um prompt de comando:

  Abra um prompt de comando e execute um dos seguintes procedimentos, dependendo da versão do Python que você deseja executar:

     ```
    # To run Python 2.7
    activate 
    python --version
    
    # To run Python 3.5
    activate py35
    python --version
    
    ```
* Use em um IDE:

  Utilize ferramentas de Python para Visual Studio (PTVS) instalados na edição de Comunidade do Visual Studio. O único ambiente configurado automaticamente no PTVS é Python 2,7.
    > [!NOTE]
    > Para apontar o PTVS no Python 3,5, você precisa criar um ambiente personalizado no PTVS. Para definir esse caminho de ambiente no Visual Studio Community Edition, vá para **ferramentas** -> **Python** -> **ambientes Python** e selecione **+ personalizado**. Em seguida, defina o local `c:\anaconda\envs\py35` como e selecione _detecção automática_.

* Use em Jupyter:

  Abra Jupyter e selecione **novo** para criar um novo bloco de anotações. Você pode definir o tipo de kernel como **Python [Conda root]** para Python 2,7 e **Python [Conda env: Py35]** para Python 3,5. 

* Instalar pacotes do Python:

  Os ambientes padrão do Python no DSVM são ambientes globais que podem ser lidos por todos os usuários. Mas somente os administradores podem gravar e instalar pacotes globais. Para instalar pacotes no ambiente global, ative para o ambiente raiz ou py35 usando o `activate` comando como administrador. Em seguida, você pode usar um Gerenciador de `conda` pacotes `pip` como ou para instalar ou atualizar pacotes.

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Versões de idioma com suporte | Microsoft R Open 3. x (100% compatível com CRAN-R)<br /> Microsoft R Server 9. x Developer Edition (uma plataforma de R pronta para empresas escalonável)|
| Edições DSVM com suporte      | Linux, Windows     |
| Como é ele configurado / instalado no DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Links para amostras      | Os notebooks Jupyter de exemplo para R estão incluídos.     |
| Ferramentas relacionadas no DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

**Windows**:

* Executar em um prompt de comando:

  Abra um prompt de comando e `R`digite.

* Use em um IDE:

  Utilize RTools para Visual Studio (RTVS) instalados na edição de Comunidade do Visual Studio ou do RStudio. Elas estão disponíveis no menu iniciar ou como um ícone de área de trabalho. 

* Usar em Jupyter

  Abra Jupyter e selecione **novo** para criar um novo bloco de anotações. Você pode definir o tipo de kernel como **R** para usar o kernel do Jupyter R (IRKernel).

* Instalar pacotes do R:

  O R é instalado no DSVM em um ambiente global que é legível por todos os usuários. Mas somente os administradores podem gravar e instalar pacotes globais. Para instalar pacotes no ambiente global, execute R usando um dos métodos anteriores. Em seguida, você pode executar o Gerenciador `install.packages()` de pacotes do R para instalar ou atualizar pacotes.

**Linux**:

* Executar no terminal:

  Abra um terminal e execute `R`.  

* Use em um IDE:

  Use o RStudio, instalado no DSVM do Linux.  

* Use em Jupyter:

  Abra Jupyter e selecione **novo** para criar um novo bloco de anotações. Você pode definir o tipo de kernel como **R** para usar o kernel do Jupyter R (IRKernel). 

* Instalar pacotes do R:

  O R é instalado no DSVM em um ambiente global que é legível por todos os usuários. Mas somente os administradores podem gravar e instalar pacotes globais. Para instalar pacotes no ambiente global, execute R usando um dos métodos anteriores. Em seguida, você pode executar o Gerenciador `install.packages()` de pacotes do R para instalar ou atualizar pacotes.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Versões de idioma com suporte | 0.6 |
| Edições DSVM com suporte      | Linux, Windows     |
| Como é ele configurado / instalado no DSVM?  | Windows: Instalado em`C:\JuliaPro-VERSION`<br /> Linux: Instalado em`/opt/JuliaPro-VERSION`    |
| Links para amostras      | Os notebooks Jupyter de exemplo para Julia estão incluídos.     |
| Ferramentas relacionadas no DSVM      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Como usá-lo e executá-lo    

**Windows**:

* Executar em um prompt de comando

  Abra um prompt de comando e `julia`execute.
* Use em um IDE:

  Use `Juno` com o IDE Julia instalado no DSVM e disponível como um atalho da área de trabalho.

* Use em Jupyter:

  Abra Jupyter e selecione **novo** para criar um novo bloco de anotações. Você pode definir o tipo de kernel como a **versão Julia**.

* Instalar pacotes Julia:

  O local Julia padrão é um ambiente global que é legível por todos os usuários. Mas somente os administradores podem gravar e instalar pacotes globais. Para instalar pacotes no ambiente global, execute Julia usando um dos métodos anteriores. Em seguida, você pode executar comandos do Gerenciador de `Pkg.add()` pacotes Julia como instalar ou atualizar pacotes.


**Linux**:
* Executar em um terminal:

  Abra um terminal e execute `julia`.
* Use em um IDE:

  Use `Juno`o, com o IDE Julia instalado no DSVM e disponível como um atalho de menu do **aplicativo** .

* Use em Jupyter:

  Abra Jupyter e selecione **novo** para criar um novo bloco de anotações. Você pode definir o tipo de kernel como a **versão Julia**.

* Instalar pacotes Julia:

  O local Julia padrão é um ambiente global que é legível por todos os usuários. Mas somente os administradores podem gravar e instalar pacotes globais. Para instalar pacotes no ambiente global, execute Julia usando um dos métodos anteriores. Em seguida, você pode executar comandos do Gerenciador de `Pkg.add()` pacotes Julia como instalar ou atualizar pacotes.

## <a name="other-languages"></a>Outros idiomas

**C#** : Disponível no Windows e acessível por meio do Visual Studio Community Edition ou no `Developer Command Prompt for Visual Studio`, em que você pode executar `csc` o comando.

**Java**: O OpenJDK está disponível nas edições Linux e Windows do DSVM e está definido no caminho. Para usar o Java, digite `javac` o `java` comando ou em um prompt de comando no Windows ou no shell bash no Linux.

**Node.js**: O Node. js está disponível nas edições Linux e Windows do DSVM e está definido no caminho. Para acessar o Node. js, digite `node` o `npm` comando ou em um prompt de comando no Windows ou no Shell do bash no Linux. No Windows, a extensão do Visual Studio para as ferramentas node. js é instalada para fornecer um IDE gráfico para desenvolver seu aplicativo node. js.

**F#** : Disponível no Windows e acessível por meio do Visual Studio Community Edition ou em `Developer Command Prompt for Visual Studio`um, no qual você pode `fsc` executar o comando.
