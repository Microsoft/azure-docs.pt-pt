---
title: Extensões para serviços em nuvem (suporte alargado)
description: Extensões para serviços em nuvem (suporte alargado)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 6db43acda679c6c1c1edd6336f693cc4757b6d45
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220922"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Extensões para serviços em nuvem (suporte alargado)

As extensões são pequenas aplicações que fornecem tarefas de configuração pós-implantação e automação em funções. Por exemplo, pode ativar uma ligação remote de ambiente de trabalho na sua função durante a implementação do serviço de cloud (suporte alargado) utilizando a extensão de ambiente de trabalho remoto.  

## <a name="remote-desktop-extension"></a>Extensão remota do ambiente de trabalho

O Ambiente de Trabalho Remoto permite-lhe aceder ao ambiente de trabalho de uma função em execução no Azure. Pode utilizar uma ligação remota no ambiente de trabalho para resolver problemas e diagnosticar problemas com a sua aplicação enquanto está em funcionamento.

Pode ativar uma ligação remota de ambiente de trabalho na sua função durante o desenvolvimento, incluindo os módulos de ambiente de trabalho remoto na definição de serviço ou através da extensão remota do ambiente de trabalho. 

Para mais informações, consulte [o ambiente de trabalho remoto Configure a partir do portal Azure](enable-rdp.md)

## <a name="windows-azure-diagnostics-extension"></a>Extensão de Diagnóstico do Windows Azure

Pode monitorizar as principais métricas de desempenho para qualquer serviço na nuvem. Cada função de serviço na nuvem recolhe dados mínimos: utilização do CPU, utilização da rede e utilização do disco. Se o serviço de nuvem tiver a extensão Microsoft.Azure.Diagnostics aplicada a uma função, essa função pode recolher pontos adicionais de dados. 

Com a monitorização básica, os dados de contador de desempenho de instâncias de função são amostrados e recolhidos em intervalos de 3 minutos. Estes dados básicos de monitorização não estão armazenados na sua conta de armazenamento e não têm qualquer custo adicional associado aos mesmos. 

Com uma monitorização avançada, as métricas adicionais são amostradas e recolhidas em intervalos de 5 minutos, 1 hora e 12 horas. Os dados agregados são armazenados numa conta de armazenamento, em tabelas, e são purgados após 10 dias. A conta de armazenamento utilizada é configurada por função; pode utilizar diferentes contas de armazenamento para diferentes funções. 

Para obter mais informações, consulte [aplicar a extensão de diagnóstico do Windows Azure em Cloud Services (suporte alargado)](enable-wad.md)

## <a name="anti-malware-extension"></a>Extensão anti malware
Uma aplicação ou serviço Azure pode permitir e configurar o Microsoft Antimalware para serviços de nuvem Azure utilizando cmdlets PowerShell. Note que o Microsoft Antimalware está instalado num estado desativado na plataforma Cloud Services que executa o Windows Server 2012 R2 e mais antigo, o que requer uma ação através de uma aplicação Azure para o permitir. Para o Windows Server 2016 e acima, o Windows Defender é ativado por padrão, pelo que estes cmdlets podem ser utilizados para configurar o Antimalware.

Para obter mais informações, consulte [adicionar o Antimalware da Microsoft ao Serviço de Nuvem Azure utilizando suporte alargado (CS-ES)](https://docs.microsoft.com/azure/security/fundamentals/antimalware-code-samples#add-microsoft-antimalware-to-azure-cloud-service-using-extended-support)

Para saber mais sobre a Azure Antimalware, visite [aqui](https://docs.microsoft.com/azure/security/fundamentals/antimalware)



## <a name="next-steps"></a>Passos seguintes 
- Reveja os [pré-requisitos](deploy-prerequisite.md) de implantação para serviços em nuvem (suporte alargado).
- Reveja [perguntas frequentes](faq.md) para serviços cloud (suporte alargado).
- Implementar um Serviço de Cloud (suporte alargado) utilizando o [portal Azure](deploy-portal.md), [PowerShell,](deploy-powershell.md) [Modelo](deploy-template.md) ou [Estúdio Visual](deploy-visual-studio.md).
