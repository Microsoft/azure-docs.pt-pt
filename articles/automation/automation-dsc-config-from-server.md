---
title: Criar configurações a partir de servidores existentes para configuração do Estado de Automação Azure
description: Este artigo diz como criar configurações a partir de servidores existentes para a Configuração do Estado da Automação Azure.
keywords: dsc,powershell,configuração,configuração
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8f376fb383e50a39f0f12d45cf9b5ae47ad6fcbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186508"
---
# <a name="create-configurations-from-existing-servers"></a>Criar configurações a partir de servidores existentes

> Aplica-se a: Windows PowerShell 5.1

Criar configurações a partir de servidores existentes pode ser uma tarefa desafiante.
Pode não querer *todas as* configurações, apenas aquelas com as quais se importa.
Mesmo assim, é necessário saber em que ordem as definições devem ser aplicadas para que a configuração se aplique com sucesso.

> [!NOTE]
> Este artigo refere-se a uma solução que é mantida pela comunidade Open Source.
> O suporte só está disponível sob a forma de colaboração gitHub, não da Microsoft.

## <a name="community-project-reversedsc"></a>Projeto comunitário: ReverseDSC

Uma solução de manutenção comunitária chamada [ReverseDSC](https://github.com/microsoft/reversedsc) foi criada para trabalhar nesta área a partir do SharePoint.

A solução baseia-se no [recurso SharePointDSC](https://github.com/powershell/sharepointdsc) e estende-a para orquestrar informações de recolha de [informação](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) dos servidores sharePoint existentes.
A versão mais recente tem [vários modos de extração](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) para determinar qual o nível de informação a incluir.

O resultado da utilização da solução é gerar dados de [configuração](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) para serem utilizados com scripts de configuração SharePointDSC.

Uma vez gerados os ficheiros de dados, pode usá-los com [scripts de configuração DSC](/powershell/scripting/dsc/overview/overview) para gerar ficheiros MOF e [fazer o upload dos ficheiros MOF para a Azure Automation](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registe os seus servidores a partir [de instalações no local](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) ou em [Azure](./automation-dsc-onboarding.md#enable-azure-vms) para puxar configurações.

Para experimentar o ReverseDSC, visite a [Galeria PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) e descarregue a solução ou clique em "Project Site" para ver a [documentação.](https://github.com/Microsoft/sharepointDSC.reverse)

## <a name="next-steps"></a>Passos seguintes

- Para compreender o PowerShell DSC, consulte a visão geral da [configuração do estado desejada do Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- Descubra os recursos da PowerShell DSC em [Recursos DSC.](/powershell/scripting/dsc/resources/resources)
- Para obter mais detalhes sobre a configuração do Gestor de Configuração Local, consulte [configurar o Gestor de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaconfig).
