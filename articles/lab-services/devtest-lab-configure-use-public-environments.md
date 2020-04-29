---
title: Configure e use ambientes públicos em Azure DevTest Labs [ Microsoft Docs
description: Este artigo descreve como configurar e usar ambientes públicos (modelos do Gestor de Recursos Azure num repo Git) em Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 127a6986e04cf90f69b2a8ec70b90b877e534708
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76721698"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Configure e use ambientes públicos em Azure DevTest Labs
A Azure DevTest Labs tem um [repositório público de modelos](https://github.com/Azure/azure-devtestlab/tree/master/Environments) do Gestor de Recursos Azure que pode usar para criar ambientes sem ter que se ligar a uma fonte externa do GitHub por si mesmo. Este repositório inclui modelos frequentemente utilizados, tais como Aplicações Web Azure, Cluster de Tecidos de Serviço e ambiente sharePoint Farm de desenvolvimento. Esta característica é semelhante ao repositório público de artefactos que está incluído para todos os laboratórios que você cria. O repositório ambiental permite-lhe começar rapidamente com modelos ambientais pré-autores com parâmetros mínimos de entrada para lhe proporcionar uma experiência de início suave para os recursos PaaS dentro de laboratórios. 

## <a name="configuring-public-environments"></a>Configuração de ambientes públicos
Como dono de laboratório, pode permitir o repositório de ambiente público para o seu laboratório durante a criação do laboratório. Para permitir ambientes públicos para o seu laboratório, selecione **On** for the **Public environments** field enquanto cria um laboratório. 

![Permitir o ambiente público para um novo laboratório](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


Para os laboratórios existentes, o repositório de ambiente público não está habilitado. Habilitar manualmente a utilização de modelos no repositório. Para laboratórios criados usando modelos de Gestor de Recursos, o repositório também é desativado por padrão.

Pode ativar/desativar ambientes públicos para o seu laboratório, e também disponibilizar apenas ambientes específicos aos utilizadores de laboratório, utilizando os seguintes passos: 

1. Selecione **Configuração e políticas** para o seu laboratório. 
2. Na secção BASES DE **MÁQUINAVirtual,** selecione **ambientes públicos.**
3. Para permitir ambientes públicos para o laboratório, selecione **Sim**. Caso contrário, selecione **Não**. 
4. Se habilitasse ambientes públicos, todos os ambientes do repositório são ativados por incumprimentos. Pode desseleccionar um ambiente para que não esteja disponível para os utilizadores do laboratório. 

![Página de ambientes públicos](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Use modelos de ambiente como um utilizador de laboratório
Como utilizador de laboratório, pode criar um novo ambiente a partir da lista ativada de modelos de ambiente, simplesmente selecionando **+Adicionar** a partir da barra de ferramentas na página de laboratório. A lista de bases inclui os modelos de ambientes públicos habilitados pela administração do seu laboratório no topo da lista.

![Modelos de ambiente público](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Passos seguintes
Este repositório é um repositório de código aberto que pode contribuir para adicionar modelos de Gestor de Recursos frequentemente utilizados e úteis. Para contribuir, basta apresentar um pedido de retirada contra o repositório.  
