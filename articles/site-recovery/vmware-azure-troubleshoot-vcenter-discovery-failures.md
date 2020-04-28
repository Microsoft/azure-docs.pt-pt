---
title: Falhas na descoberta do VMware VCenter em Recuperação do Site Azure
description: Este artigo descreve como resolver falhas de descoberta vCenter vCenter em Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: f00c7b12accde9df9a5708a2b8b378d70428318d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74091241"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>Falhas na descoberta do Servidor de Problemas vCenter Server

Este artigo ajuda-o a resolver problemas que ocorrem devido a falhas na descoberta vCenter vMware VMware.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Valores não numéricos na propriedade maxSnapShots

Em versões anteriores às 9.20, o vCenter desliga-se `snapshot.maxSnapShots` quando recupera um valor não numérico para a propriedade num VM.

Este problema é identificado pelo erro ID 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Para resolver a questão:

- Identifique o VM e detete teo valor para um valor numérico (definições de Edição VM no vCenter).

Ou

- Atualize o seu servidor de configuração para a versão 9.20 ou mais tarde.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problemas de configuração proxy para conectividade vCenter

vCenter Discovery honra as definições de proxy predefinidos do Sistema configuradas pelo utilizador do Sistema. O serviço DRA honra as definições de procuração fornecidas pelo utilizador durante a instalação do servidor de configuração utilizando o instalador de configuração unificado ou o modelo OVA. 

Em geral, o representante é utilizado para comunicar às redes públicas; como comunicar com Azure. Se o proxy estiver configurado e o vCenter estiver num ambiente local, não será capaz de comunicar com a DRA.

As seguintes situações ocorrem quando esta questão é encontrada:

- O servidor \<vCenter vCenter> não é acessível devido ao erro: O servidor remoto devolveu um erro: (503) Servidor Indisponível
- O servidor \<vCenter vCenter> não é alcançável devido ao erro: O servidor remoto devolveu um erro: Incapaz de se ligar ao servidor remoto.
- Incapaz de ligar ao servidor vCenter/ESXi.

Para resolver a questão:

Descarregue a [ferramenta PsExec](https://aka.ms/PsExec). 

Utilize a ferramenta PsExec para aceder ao contexto do utilizador do Sistema e determinar se o endereço proxy está configurado. Em seguida, pode adicionar vCenter à lista de bypass utilizando os seguintes procedimentos.

Para a configuração de procuração Discovery:

1. Abra o IE no contexto do utilizador do sistema utilizando a ferramenta PsExec.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Modifique as definições de procuração no Internet Explorer para contornar o endereço IP vCenter.
3. Reinicie o serviço de tmanssvc.

Para a configuração de procuração DRA:

1. Abra um pedido de comando e abra a pasta Microsoft Azure Site Recovery Provider.
 
    **cd C:\Program Files\Microsoft Azure Site Recovery Provider**

3. A partir do pedido de comando, executar o seguinte comando.
   
   **DRCONFIGURATOR. EXE /configure /AddBypassUrls [Endereço IP/FQDN do vCenter Server fornecido no momento do add vCenter]**

4. Reinicie o serviço de fornecedor de DRA.

## <a name="next-steps"></a>Passos seguintes

[Gerir o servidor de configuração para recuperação de desastres VMware VM](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
