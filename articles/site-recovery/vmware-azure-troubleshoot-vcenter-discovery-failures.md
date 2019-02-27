---
title: Reativação pós-falha para o local de resolução de problemas durante a recuperação após desastre de VM de VMware para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve as opções para solucionar problemas de reativação pós-falha e a nova proteção durante a recuperação após desastre de VM de VMware para o Azure com o Azure Site Recovery.
author: vDonGlover
manager: JarrettRenshaw
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: v-doglov
ms.openlocfilehash: a0707ad356e51a9e4d4f73c54085fe9736bb0752
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885299"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>Resolver problemas de falhas de deteção do vCenter

Este artigo ajuda-o a resolver problemas que ocorrem devido a falhas de deteção do vCenter VMware.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Valores não numéricos na propriedade maxSnapShots

Em versões anteriores à 9.20, vCenter desliga quando obtém um valor de não-numérica para a propriedade `snapshot.maxSnapShots` propriedade numa VM.

Este problema é identificado pelo ID de erro 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Para resolver o problema:

- Identifique a VM e defina o valor como um valor numérico (VM editar definições no vCenter).

Ou

- Atualize o servidor de configuração para a versão 9.20 ou posterior.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problemas de configuração de proxy para a conectividade do vCenter

vCenter deteção respeita as definições de proxy do sistema predefinida configuradas pelo utilizador do sistema. O serviço DRA respeita as definições de proxy fornecidas pelo utilizador durante a instalação do servidor de configuração com o instalador unificado do programa de configuração ou o modelo de OVA. 

Em geral, o proxy é utilizado para comunicar a redes públicas; Por exemplo, a comunicação com o Azure. Se o proxy está configurado e vCenter está num ambiente local, não será capaz de comunicar com o DRA.

As seguintes situações ocorrerem quando é encontrado este problema:

- O servidor vCenter <vCenter> não está acessível devido ao erro: O servidor remoto devolveu um erro: (503) Server Unavailable
- O servidor vCenter <vCenter> não está acessível devido ao erro: O servidor remoto devolveu um erro: Não é possível ligar ao servidor remoto.
- Não é possível ligar ao servidor do vCenter/ESXi.

Para resolver o problema:

Transfira o [PsExec ferramenta](https://aka.ms/PsExec). 

Utilize a ferramenta PsExec para acessar o contexto de utilizador do sistema e determinar se o endereço de proxy está configurado. Em seguida, pode adicionar o vCenter para a lista de omissão utilizando os procedimentos seguintes.

Para a configuração de proxy de Deteção:

1. Abra IE no contexto de utilizador do sistema usando a ferramenta PsExec.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Modifica as definições de proxy no Internet Explorer para ignorar o endereço IP do vCenter.
3. Reinicie o serviço de tmanssvc.

Para a configuração de proxy do DRA:

1. Abra um prompt de comando e abra a pasta do Microsoft Azure Site Recovery Provider.
 
    **CD C:\Program Files\Microsoft Azure Site Recovery Provider**

3. Na linha de comandos, execute o seguinte comando.
   
   **DRCONFIGURATOR. EXE / configurar /AddBypassUrls [endereço IP/FQDN do vCenter Server fornecidas no momento da adicionar vCenter]**

4. Reinicie o serviço fornecedor do DRA.

## <a name="next-steps"></a>Passos Seguintes

[Gerir o servidor de configuração para a recuperação de desastres da VM de VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
