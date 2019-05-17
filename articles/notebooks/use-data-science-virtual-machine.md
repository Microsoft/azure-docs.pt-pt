---
title: Utilize as máquinas de virtuais de ciência de dados do Azure
description: Ligue-se para uma Máquina Virtual do Azure de ciência de dados (DSVM) para expandir o poder de computação disponível para blocos de notas do Azure.
services: app-service
documentationcenter: ''
author: getroyer
manager: andneil
ms.assetid: 0ccc2529-e17f-4221-b7c7-9496d6a731cc
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: getroyer
ms.openlocfilehash: d4321fe60d47bf942e2c413ba81c2c9f54317fd1
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597870"
---
# <a name="use-azure-data-science-virtual-machines"></a>Utilize as máquinas de virtuais de ciência de dados do Azure

Por predefinição, os projetos de executar no **computação gratuita** escalão, o que está limitado a 4 GB de memória e 1 GB de dados para evitar abusos. Pode ignorar estas limitações ao utilizar uma máquina virtual diferente que já aprovisionados numa subscrição do Azure. Para esse fim, a melhor opção é uma Máquina Virtual de ciência de dados do Azure (DSVM) com o **Máquina Virtual de ciência de dados para Linux (Ubuntu)** imagem. Tal uma DSVM vem pré-configurada com tudo o que precisa para blocos de notas do Azure e é apresentado automaticamente no **executar** na lista pendente em blocos de notas do Azure.

> [!Note]
> Blocos de notas do Azure só é suportada em DSVMs criadas com a imagem de Linux Ubuntu on. Blocos de notas não são suportados nas imagens do Windows 2012, Windows 2016 ou CentOS do Linux.

## <a name="create-a-dsvm-instance"></a>Criar uma instância DSVM

Para criar uma nova instância DSVM, siga as instruções [criar uma VM de ciência de dados do Ubuntu](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Para obter mais informações, incluindo os detalhes dos preços, consulte [máquinas de virtuais de ciência de dados](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Ligar a DSVM

Assim que for criado a DSVM, selecione o **executar** na lista pendente nos blocos de notas do Azure dashboard do projeto e selecione a instância DSVM apropriada. A lista pendente mostra instâncias DSVM se as condições seguintes forem verdadeiras:

- Tem sessão iniciada em blocos de notas do Azure com uma conta que utiliza o Azure Active Directory (AAD), como uma conta da empresa.
- Sua conta está ligada a uma subscrição do Azure.
- Tiver uma ou mais máquinas virtuais nessa subscrição, com, pelo menos, acesso de leitor, que utiliza a máquina de Virtual de ciência de dados de imagem do Linux (Ubuntu).)

![Instâncias de Máquina Virtual de ciência de dados na lista pendente no dashboard do projeto](media/project-compute-tier-dsvm.png)

Quando seleciona uma instância DSVM, blocos de notas do Azure pode solicitar-lhe as credenciais de máquina específica que utilizou quando criou a VM.

Se qualquer uma das condições não forem cumpridas, ainda pode ligar ao DSVM. Na lista pendente, selecione o **computação direto** opção, que pede-lhe um nome (para mostrar na lista), endereço IP da VM e porta (normalmente, a porta predefinida para o qual escuta JupyterHub, 8000) e as credenciais VM:

![Linha de comandos para recolher informações do servidor para a opção de computação direto](media/project-compute-tier-direct.png)

Obter estes valores a partir da página da DSVM no portal do Azure.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Aceder a ficheiros de blocos de notas do Azure da DSVM

Para preservar a paridade de caminhos de arquivo com o **computação gratuita** escalão, é possível apenas abrir um projeto ao mesmo tempo numa DSVM do. Para abrir um novo projeto, tem de encerrar o projeto aberto pela primeira vez.

![Botão de encerramento em blocos de notas do Azure](media/shutdown.png)

Quando um projeto é executado numa VM, os ficheiros estão montados no diretório raiz do servidor do Jupyter (o diretório mostrado na JupyterHub), substituindo os arquivos de blocos de notas do Azure padrão. Quando encerrar a VM com o **encerramento** botão do bloco de notas da interface do Usuário, blocos de notas do Azure restaura os arquivos padrão.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as DSVMs no [introdução ao Azure dados máquinas virtuais de ciência](/machine-learning/data-science-virtual-machine/overview).
