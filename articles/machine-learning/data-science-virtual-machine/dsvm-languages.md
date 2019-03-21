---
title: Idiomas suportados para a máquina de Virtual de ciência de dados
titleSuffix: Azure
description: Saiba mais sobre os idiomas de programa e ferramentas relacionadas que são previamente instaladas na máquina de Virtual de ciência de dados.
keywords: ferramentas de ciência de dados, máquina de virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 883e1c11e3485c0bf3441a67a3a191563ea3aed4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57990790"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Idiomas suportados na máquina de Virtual de ciência de dados 

A Máquina Virtual de ciência de dados (DSVM) é fornecido com várias linguagens previamente criadas e ferramentas de desenvolvimento para criar as suas aplicações de IA. Aqui estão alguns da evidência aqueles. 

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 Edition)

|    |           |
| ------------- | ------------- |
| Versões de idiomas suportados | 2.7 e 3.6 |
| Edições DSVM suportados      | Windows Server 2016     |
| Como é ele configurado / instalado no DSVM?  | Dois global `conda` os ambientes são criados. <br /> * `root` ambiente localizado em `/anaconda/` é o Python 3.6. <br/> * `python2` ambiente localizado em `/anaconda/envs/python2`é o Python 2.7       |
| Links para amostras      | Blocos de notas Jupyter do exemplo de Python são incluídos     |
| Ferramentas relacionadas na DSVM      | PySpark, R, Julia      |

> [!NOTE]
> Windows Server 2016 criada antes de Março de 2018 contém o Python 3.5 e o Python 2.7. Também o Python 2.7 é o conda **raiz** ambiente e **py35** é o ambiente de Python 3.5. 

### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?    

* Em execução na linha de comandos

Abra a linha de comandos e faça o seguinte, consoante a versão do Python que pretende executar. 

```
# To run Python 2.7
activate python2
python --version

# To run Python 3.6
activate 
python --version

```
* Usando num IDE

Utilize ferramentas de Python para Visual Studio (PTVS) instalados na edição de Comunidade do Visual Studio. Configuração do ambiente apenas automaticamente no PTVS por predefinição é Python 3.6. 

> [!NOTE]
> Para apontar as PTVS para Python 2.7, terá de criar um ambiente personalizado no PTVS. Para definir esta caminhos de ambiente no Visual Studio Community Edition, navegue para **ferramentas** -> **ferramentas do Python** -> **ambientes do Python** e, em seguida, clique em **+ personalizado**. Em seguida, defina a localização `c:\anaconda\envs\python2` e, em seguida, clique em _deteção automática_. 

* Utilizar o Jupyter

Abra o Jupyter e clique no `New` botão para criar um novo bloco de notas. Neste momento, pode escolher o tipo de kernel como _Python [Conda raiz]_ para o Python 3.6 e _Python [Conda env:python2]_ para o ambiente de Python 2.7. 

* Instalar pacotes de Python

Os ambientes de Python padrão na DSVM são ambiente global legível por todos os utilizadores. Mas apenas os administradores podem escrever / instalar pacotes global. Para instalar o pacote para o ambiente global, ativar para a raiz ou python2 ambiente utilizando o `activate` comando como administrador. Pode utilizar o Gestor de pacotes, como `conda` ou `pip` para instalar ou atualizar os pacotes. 

## <a name="python-linux-and-windows-server-2012-edition"></a>Python (Linux e o Windows Server 2012 Edition)

|    |           |
| ------------- | ------------- |
| Versões de idiomas suportados | 2.7 e 3.5 |
| Edições DSVM suportados      | Linux, Windows Server 2012    |
| Como é ele configurado / instalado no DSVM?  | Dois global `conda` os ambientes são criados. <br /> * `root` ambiente localizado em `/anaconda/` é o Python 2.7. <br/> * `py35` ambiente localizado em `/anaconda/envs/py35`é Python 3.5       |
| Links para amostras      | Blocos de notas Jupyter do exemplo de Python são incluídos     |
| Ferramentas relacionadas na DSVM      | PySpark, R, Julia      |
### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?    

**Linux**
* Em execução no terminal

Abra o terminal e efetue o seguinte, consoante a versão do Python que pretende executar. 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* Usando num IDE

Utilize PyCharm instalada na edição de Comunidade do Visual Studio. 

* Utilizar o Jupyter

Abra o Jupyter e clique no `New` botão para criar um novo bloco de notas. Neste momento, pode escolher o tipo de kernel como _Python [Conda raiz]_ para o Python 2.7 e _Python [Conda env:py35]_ para o ambiente de Python 3.5. 

* Instalar pacotes de Python

Os ambientes de Python padrão na DSVM são ambientes global legíveis por todos os utilizadores. Mas apenas os administradores podem escrever / instalar pacotes global. Para instalar o pacote para o ambiente global, ativar para a raiz ou py35 ambiente utilizando o `source activate` comando como administrador ou um utilizador com permissão de sudo. Pode utilizar um Gestor de pacotes, como `conda` ou `pip` para instalar ou atualizar os pacotes. 

**Windows 2012**
* Em execução na linha de comandos

Abra a linha de comandos e faça o seguinte, consoante a versão do Python que pretende executar. 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* Usando num IDE

Utilize ferramentas de Python para Visual Studio (PTVS) instalados na edição de Comunidade do Visual Studio. Configuração do ambiente apenas automaticamente no PTVS no Python 2.7. 
> [!NOTE]
> Para apontar as PTVS Python 3.5, terá de criar um ambiente personalizado no PTVS. Para definir esta caminhos de ambiente no Visual Studio Community Edition, navegue para **ferramentas** -> **ferramentas do Python** -> **ambientes do Python** e, em seguida, clique em **+ personalizado**. Em seguida, defina a localização `c:\anaconda\envs\py35` e, em seguida, clique em _deteção automática_. 

* Utilizar o Jupyter

Abra o Jupyter e clique no `New` botão para criar um novo bloco de notas. Neste momento, pode escolher o tipo de kernel como _Python [Conda raiz]_ para o Python 2.7 e _Python [Conda env:py35]_ para o ambiente de Python 3.5. 

* Instalar pacotes de Python

Os ambientes de Python padrão na DSVM são ambiente global legível por todos os utilizadores. Mas apenas os administradores podem escrever / instalar pacotes global. Para instalar o pacote para o ambiente global, ativar para a raiz ou py35 ambiente utilizando o `activate` comando como administrador. Pode utilizar o Gestor de pacotes, como `conda` ou `pip` para instalar ou atualizar os pacotes. 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Versões de idiomas suportados | Microsoft R Open 3.x (100% compatível com R do CRAN<br /> Edição de programador do Microsoft R Server 9.x (uma empresa escalonável pronta R plataforma)|
| Edições DSVM suportados      | Linux, Windows     |
| Como é ele configurado / instalado no DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: ` /usr/lib64/microsoft-r/3.3/lib64/R`    |
| Links para amostras      | Blocos de notas Jupyter do exemplo de R estão incluídos     |
| Ferramentas relacionadas na DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?    

**Windows**:

* Em execução na linha de comandos

Abra o prompt de comando e digitar `R`.

* Usando num IDE

Utilize RTools para Visual Studio (RTVS) instalados na edição de Comunidade do Visual Studio ou do RStudio. Estas estão disponíveis no menu Iniciar ou como um ícone de área de trabalho. 

* Utilizar o Jupyter

Abra o Jupyter e clique no `New` botão para criar um novo bloco de notas. Neste momento, pode escolher o tipo de kernel como _R_ para utilizar o kernel de Jupyter R (IRKernel). 

* Instalar pacotes de R

R estiver instalado no DSVM num ambiente global legível por todos os utilizadores. Mas apenas os administradores podem escrever / instalar pacotes global. Para instalar o pacote para o ambiente global, execute o R através de um dos métodos acima. Em seguida, pode executar o Gestor de pacotes de R `install.packages()` para instalar ou atualizar os pacotes. 

**Linux**:

* Em execução no terminal

Abra o terminal e execute apenas `R`.  

* Usando num IDE

Utilize o r Studio instalado na DSVM do Linux.  

* Utilizar o Jupyter

Abra o Jupyter e clique no `New` botão para criar um novo bloco de notas. Neste momento, pode escolher o tipo de kernel como _R_ para utilizar o kernel de Jupyter R (IRKernel). 

* Instalar pacotes de R

R estiver instalado no DSVM num ambiente global legível por todos os utilizadores. Mas apenas os administradores podem escrever / instalar pacotes global. Para instalar o pacote para o ambiente global, execute o R através de um dos métodos acima. Em seguida, pode executar o Gestor de pacotes de R `install.packages()` para instalar ou atualizar os pacotes. 


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Versões de idiomas suportados | 0.6 |
| Edições DSVM suportados      | Linux, Windows     |
| Como é ele configurado / instalado no DSVM?  | Windows: Instalado em `C:\JuliaPro-VERSION`<br /> Linux: Instalado em `/opt/JuliaPro-VERSION`    |
| Links para amostras      | Blocos de notas do Jupyter exemplo para Leonor estão incluídos     |
| Ferramentas relacionadas na DSVM      | Python, R      |
### <a name="how-to-use--run-it"></a>Como utilizar / executá-lo?    

**Windows**:

* Em execução na linha de comandos

Abra a linha de comandos e apenas execute `julia`. 
* Usando num IDE

Utilize `Juno` IDE Julia instalado na DSVM e disponível como um atalho de desktop.

* Utilizar o Jupyter

Abra o Jupyter e clique no `New` botão para criar um novo bloco de notas. Neste momento, pode escolher o tipo de kernel como `Julia VERSION` 

* Instalar pacotes de Julia

A Leonor localização predefinida é um ambiente global legível por todos os utilizadores. Mas apenas os administradores podem escrever / instalar pacotes global. Para instalar o pacote para o ambiente global, execute Leonor através de um dos métodos acima. Em seguida, pode executar o pacote de Julia manager comandos como `Pkg.add()` para instalar ou atualizar os pacotes. 


**Linux**:
* Em execução no terminal.

Abra o terminal e execute apenas `julia`. 
* Usando num IDE

Utilize `Juno` IDE Julia instalado na DSVM e disponível como um atalho de menu do aplicativo.

* Utilizar o Jupyter

Abra o Jupyter e clique no `New` botão para criar um novo bloco de notas. Neste momento, pode escolher o tipo de kernel como `Julia VERSION` 

* Instalar pacotes de Julia

A Leonor localização predefinida é um ambiente global legível por todos os utilizadores. Mas apenas os administradores podem escrever / instalar pacotes global. Para instalar o pacote para o ambiente global, execute Leonor através de um dos métodos acima. Em seguida, pode executar o pacote de Julia manager comandos como `Pkg.add()` para instalar ou atualizar os pacotes. 

## <a name="other-languages"></a>Outros idiomas

**C#**: Disponível no Windows e acessível através da edição de Comunidade do Visual Studio ou num `Developer Command Prompt for Visual Studio` onde pode executar apenas `csc` comando. 

**Java**: OpenJDK está disponível na edição de Linux e Windows da DSVM e conjunto no caminho. Pode digitar `javac` ou `java` comando no prompt de comando no Windows ou no shell de bash no Linux utilizar o Java. 

**NODE. js**: node. js está disponível na edição de Linux e Windows da DSVM e conjunto no caminho. Pode digitar `node` ou `npm` comando no prompt de comando no Windows ou no shell de bash no Linux para acessar o node. js. No Windows, as ferramentas do node. js para a extensão do Visual Studio está instalado para fornecer um IDE gráfico para desenvolver a sua aplicação node. js. 

**F#**: Disponível no Windows e acessível através da edição de Comunidade do Visual Studio ou num `Developer Command Prompt for Visual Studio` onde pode executar apenas `fsc` comando. 


