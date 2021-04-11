---
title: Implementar Micro Focus Enterprise Server 5.0 a AKS | Microsoft Docs
description: Reenquadiço as suas cargas de trabalho de computador principal IBM z/OS utilizando o ambiente de desenvolvimento e teste do Micro Focus em máquinas virtuais Azure (VMs).
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 58ba530e434a9dc141ba8cb98120f6325eb06f7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950478"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>Implementar Micro Focus Enterprise Server 5.0 a AKS

Num outro [artigo,](./run-enterprise-server-container.md)delineei os passos para executar o Micro Focus Enterprise Server 5.0 num contentor docker. Como seguimento a isso, gostaria de lhe mostrar como dar um passo mais além e implementar a imagem do Docker que criou para o Serviço Azure Kubernetes (AKS).

Azure Kubernetes Service é um serviço de orquestração gerido baseado em Kubernetes. Permite-lhe implantar, escalar e gerir contentores Docker (e outras aplicações à base de contentores) através de um conjunto de hospedeiros de contentores.

Este é um processo em três etapas. Tem de:

1.  Crie um registo de contentores Azure para armazenar a sua imagem docker.

2.  Crie um cluster Azure Kubernetes para executar a imagem do Docker.

3.  Execute a aplicação.

Isto permite-lhe escalar (e reduzir) as suas cargas de carga de modernização do mainframe em Azure, tirando partido da plataforma cloud.

Está pronto? Vamos começar!

## <a name="create-the-azure-container-registry"></a>Criar o Registo de Contentores Azure

A partir do portal Azure, **selecione Criar um recurso** no canto superior esquerdo. A partir do Painel de Mercado, selecione **Contentores e,** em seguida, **Registo de Contentores**. Isto leva-o ao painel de registo de **contentores Create** onde precisa de preencher o **nome de Registo**, Assinatura **Azure,** **Grupo de Recursos** e **Localização.** O **nome do registo** precisa de ser resolvido, por isso deve ser único. Selecione o **grupo de Recursos** que utilizou no post de blog anterior e na mesma **Localização** correspondente. Selecione **Ativar** para **utilizador de administração** e **Básico** para o **SKU**. Assim que tiver tudo preenchido, **selecione Criar**.

![Criar interface de registo de contentores](media/deploy-image-1.png)

Depois de o registo ter sido implantado, selecione **Ir para o Recurso**. Isto leva-o à lâmina principal para o Registo do Contentor Azure. Uma boa funcionalidade aqui é a opção de menu **Quick Start.** Selecione-o e verá instruções para o que precisa ser feito para empurrar e puxar imagens de e para o registo. Vamos embora estes:

1.  **Instale o Docker** – Não precisa de se preocupar com isto porque já está feito.

2.  **Executar a imagem base "olá – mundo"** – Mais uma vez, não é necessário, mas sinta-se à vontade para experimentar.

3.  **Faça login no seu registo de contentores** – É necessário fazê-lo a partir da máquina virtual (VM). Copie o comando para a área de transferência ou bloco de notas.

    Para o registo que criei, o comando é: **estivador acrmf50.azurecr.io**

4.  **Empurre para o seu registo** – Tem de o fazer para a imagem Micro Focus também uma vez que tenha iniciado sessão no VM.

5.  **Retire do seu registo** – Não é relevante para este walk-through, mas é bom saber se precisa de executar outra imagem do Docker.

Antes de sair do portal, precisa de obter as credenciais para o registo para que possa fazer login. Saia da lâmina **'Arranque Rápido'** e selecione **as teclas** de acesso do Menu registo. Copie o **nome de utilizador** e uma das **Palavras-Passe** (existem duas) para a área de transferência ou bloco de notas. Vai precisar deles mais tarde para fazer login.

Agora que sabe o que tem de ser feito, faça login no VM.

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>RDP para a máquina virtual que usou para criar a imagem docker

Uma vez que já criou a imagem do Docker num Servidor Windows 2016, precisa de iniciar sessão nesse VM. A partir deste VM, pode empurrar a imagem para o Registo de Contentores Azure que acabou de criar. Navegue para o VM no portal Azure e, em seguida, selecione **Overview** e, em seguida, **Conecte-se**. Isto liga-o ao VM através de um protocolo de ambiente de trabalho remoto (PDR). Tens de usar as credenciais de quando criaste o VM.

## <a name="log-in-and-push-the-image-to-the-registry"></a>Faça login e empurre a imagem para o registo

Uma vez iniciado o login, abra um pedido de comando e inicie os seguintes comandos Docker:

-   **imagens docker** – isto mostra uma lista de todas as imagens atualmente instaladas no VM. Tome nota do **microfocus/es-acctdemo** porque é com este que vai trabalhar.

-   **acrmf50.azurecr.io de login do docker** – O formato correto aqui é o login *\<registry name\> do Docker*. Substitua o nome que usou quando criou o registo.

    -   Necessitará do **nome de utilizador** e **palavra-passe** que copiou do portal Azure. Deverá ver uma imagem semelhante à seguinte.

    ![Screenshot de um pedido de comando do administrador](media/deploy-image-2.png)

-   **estivador tag microfocus/es-acctdemo acrmf50.azurecr.io/es-acctdemo** – Esta marca a imagem apropriada com o nome do repositório. **NOTA:** Se o nome \<microfocus/es-acctdemo\> não funcionar, tente utilizar o ID de imagem completo. Depois de executar o comando, **escreva imagens de estivador - sem trunc**. Devia ver algo como a próxima imagem. Note que a imagem está devidamente marcada.

    ![Selecione o ecrã de pedido de comando do administrador](media/deploy-image-3.png)

-   **estivador empurrar acrmf50.azurecr.io/es-acctdemo** – Isto dá o empurrão real para o seu repositório. Porque o tamanho é de 15 GB, leva alguns minutos para correr. Se tudo correr bem, deve ver algo como a seguinte imagem.

    ![Ecrã de solicitação de comando do administrador](media/deploy-image-4.png)

Agora volte ao portal Azure, especificamente ao **Repositório.** No menu do **Repositório**, **selecione Repositórios,** e deverá ver **a lista de es-acctdemo.** Agora crie o cluster AKS.

## <a name="create-the-azure-kubernetes-aks-cluster"></a>Criar o cluster Azure Kubernetes (AKS)

A partir do portal Azure, **selecione Criar um recurso** e, em seguida, o Serviço De **Contentores /Kubernetes** a partir do menu **Marketplace.** Em seguida, tem de preencher a lâmina de **cluster Create Kubernetes.** Certifique-se de manter o cluster na mesma região e grupo de recursos que tem usado. Pode aceitar o resto dos incumprimentos, exceto a **contagem de nó,** que só precisa de ser 1. Quando terminar, selecione **Rever + criar**.

![Criar ecrã de cluster Kubernetes](media/deploy-image-5.png)

Quando estiver concluída, a implementação colocará os artefactos **do Serviço Kubernetes** no **grupo de Recursos** selecionado na lâmina. No entanto, o cluster real terá o seu próprio grupo de recursos criado durante a implantação. Se selecionar **grupos** de recursos do menu à esquerda, pode encontrá-lo com base na convenção de nomeação. Aqui está uma imagem minha- é a última da lista.

![Screenshot dos grupos de recursos](media/deploy-image-6.png)

**Executar a imagem**

Agora é hora de puxar a imagem e executá-la em AKS. A maneira mais fácil de fazer isso a partir do portal Azure é usar a Cloud Shell. Encontrará o ícone no topo direito do portal Azure. Note que para esta passagem, estou usando a Carapaça Bash.

![Screenshot do ícone cloud shell](media/deploy-image-7.png)

Uma vez carregada a concha, digite o seguinte comando:

**kubectl run es-acctdemo --imagem acrmf50.azurecr.io/es-acctdemo --porta=9040**

Isto retira a imagem do repositório **acrmf50.azurecr.io** e coloca-a em AKS. Em seguida, executa a imagem com porta 9040 aberta. Deve lembrar-se que esta era a porta que tinha aberto para a sua imagem do Docker. Precisa disso para aceder ao Servidor da Empresa.

Kubernetes deve responder com uma mensagem de que a implementação foi criada.

![Screenshot de uma mensagem de implantação](media/deploy-image-8.jpg)

Para ver se o recipiente está realmente em funcionamento, escreva: **kubectl get pods**.

Você deve ver es-acctdemo como uma vagem de corrida, como na imagem seguinte.

![Screenshot es-acctdemo como uma cápsula de corrida](media/deploy-image-9.png)

Parabéns! Está agora a executar o Enterprise Server no Serviço Azure Kubernetes. No próximo artigo, vou mostrar-lhe como aceder à Consola Administrativa do Servidor Empresarial e também como aproveitar kubernetes para escalar a sua implementação.
