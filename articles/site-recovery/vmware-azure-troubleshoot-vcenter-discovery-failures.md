---
title: Solucionar falhas de descoberta do VMware vCenter no Azure Site Recovery
description: Este artigo descreve como solucionar problemas de falhas de descoberta do VMware vCenter no Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: f00c7b12accde9df9a5708a2b8b378d70428318d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091241"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>Solucionar problemas vCenter Server falhas de descoberta

Este artigo ajuda você a solucionar problemas que ocorrem devido a falhas de descoberta do VMware vCenter.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Valores não numéricos na propriedade maxSnapShots

Nas versões anteriores à 9,20, o vCenter se desconecta quando recupera um valor não numérico para a propriedade `snapshot.maxSnapShots` Propriedade em uma VM.

Esse problema é identificado pela ID de erro 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Para resolver o problema:

- Identifique a VM e defina o valor como um valor numérico (configurações de edição de VM no vCenter).

Ou

- Atualize o servidor de configuração para a versão 9,20 ou posterior.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problemas de configuração de proxy para conectividade do vCenter

a descoberta do vCenter honra as configurações de proxy padrão do sistema definidas pelo usuário do sistema. O serviço DRA honra as configurações de proxy fornecidas pelo usuário durante a instalação do servidor de configuração usando o modelo OVA ou o instalador de instalação unificado. 

Em geral, o proxy é usado para se comunicar com redes públicas; como a comunicação com o Azure. Se o proxy estiver configurado e o vCenter estiver em um ambiente local, ele não poderá se comunicar com o DRA.

As seguintes situações ocorrem quando esse problema é encontrado:

- O servidor do vCenter \<o > do vCenter não está acessível devido ao erro: o servidor remoto retornou um erro: (503) servidor não disponível
- O servidor do vCenter \<o > do vCenter não está acessível devido ao erro: o servidor remoto retornou um erro: não é possível conectar-se ao servidor remoto.
- Não é possível conectar-se ao servidor vCenter/ESXi.

Para resolver o problema:

Baixe a [ferramenta PsExec](https://aka.ms/PsExec). 

Use a ferramenta PsExec para acessar o contexto de usuário do sistema e determinar se o endereço do proxy está configurado. Em seguida, você pode adicionar o vCenter à lista de bypass usando os procedimentos a seguir.

Para a configuração de proxy de descoberta:

1. Abra o IE no contexto do usuário do sistema usando a ferramenta PsExec.
    
    PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"

2. Modifique as configurações de proxy no Internet Explorer para ignorar o endereço IP do vCenter.
3. Reinicie o serviço tmanssvc.

Para configuração de proxy DRA:

1. Abra um prompt de comando e abra a pasta Microsoft Azure Site Recovery provedor.
 
    **CD C:\Arquivos de Programas\microsoft Azure Site Recovery Provider**

3. No prompt de comando, execute o comando a seguir.
   
   **DRCONFIGURATOR. EXE/configure/AddBypassUrls [endereço IP/FQDN do vCenter Server fornecido no momento da adição do vCenter]**

4. Reinicie o serviço do provedor DRA.

## <a name="next-steps"></a>Passos seguintes

[Gerenciar o servidor de configuração para recuperação de desastre de VM VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
