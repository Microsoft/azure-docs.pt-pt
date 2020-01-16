---
title: Configuração baseada em STIG para usar na configuração de estado – automação do Azure
description: Saiba mais sobre as configurações baseadas em STIG para configuração de estado na automação do Azure.
keywords: DSC, PowerShell, configuração, instalação
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 275b3bd25f931b73e8a378433899ef9ade4d47c1
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028269"
---
# <a name="configuration-based-on-stig"></a>Configuração baseada em STIG

> Aplica-se a: Windows PowerShell 5,1

Criar conteúdo de configuração pela primeira vez pode ser desafiador.
Em muitos casos, o objetivo é automatizar a configuração de servidores após uma "linha de base" que esperamos alinhar-se a uma recomendação do setor.

> [!NOTE]
> Este artigo refere-se a uma solução mantida pela comunidade de código-fonte aberto.
> O suporte está disponível apenas na forma de colaboração do GitHub, não da Microsoft.

## <a name="community-project-powerstig"></a>Projeto da Comunidade: PowerSTIG

Um projeto de comunidade chamado [PowerSTIG](https://github.com/microsoft/powerstig) tem como objetivo resolver esse problema gerando conteúdo DSC com base em [informações públicas](https://public.cyber.mil/stigs/) fornecidas sobre o Stig (guia de implementação técnica de segurança),

Lidar com linhas de base é mais complicado do que parece.
Muitas organizações precisam [documentar exceções](https://github.com/microsoft/powerstig#powerstigdata) às regras e gerenciar esses dados em escala.
O PowerSTIG resolve o problema fornecendo [recursos de composição](https://github.com/microsoft/powerstig#powerstigdsc) para atender a cada área da configuração, em vez de tentar resolver todo o intervalo de configurações em um arquivo grande.

Depois que as configurações tiverem sido geradas, você poderá usar os [scripts de configuração de DSC](/powershell/scripting/dsc/configurations/configurations) para gerar arquivos MOF e [carregar os arquivos MOF para a automação do Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Em seguida, registre os servidores no [local](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) ou [no Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) para efetuar pull das configurações.

Para experimentar o PowerSTIG, visite a [Galeria do PowerShell](https://www.powershellgallery.com) e baixe a solução ou clique em "site do projeto" para exibir a [documentação](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da configuração de estado desejado do Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Recursos de DSC](/powershell/scripting/dsc/resources/resources)
- [Configurando o Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaconfig)
