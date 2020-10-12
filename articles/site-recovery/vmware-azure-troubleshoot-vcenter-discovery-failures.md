---
title: Falhas na descoberta do VMware vCenter em Recuperação de Sítios Azure
description: Este artigo descreve como resolver problemas falhas de descoberta vCenter VMware na Recuperação do Site Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: d333972ea5f74d1676e5e4b4e1417c6bf5d87b79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135344"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>Falhas de descoberta do servidor vCenter resolução de problemas

Este artigo ajuda-o a resolver problemas que ocorrem devido a falhas na descoberta do VMware vCenter.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Valores não numéricos na propriedade maxSnapShots

Nas versões anteriores às 9.20, o vCenter desliga-se quando recupera um valor não numérico para a  `snapshot.maxSnapShots` propriedade num VM.

Este problema é identificado por erro ID 95126.

```output
ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
System.FormatException: Input string was not in a correct format.
    at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
    at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
    at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
```

Para resolver o problema:

- Identifique o VM e defina o valor para um valor numérico (definições de Edição VM em vCenter).

Ou

- Atualize o seu servidor de configuração para a versão 9.20 ou posterior.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problemas de configuração proxy para a conectividade vCenter

vCenter Discovery honra as definições de procuração padrão do Sistema configuradas pelo utilizador do Sistema. O serviço DRA honra as definições de procuração fornecidas pelo utilizador durante a instalação do servidor de configuração utilizando o instalador de configuração unificado ou o modelo OVA. 

Em geral, o representante é utilizado para comunicar a redes públicas; como comunicar com Azure. Se o proxy estiver configurado e o vCenter estiver num ambiente local, não será capaz de comunicar com a DRA.

As seguintes situações ocorrem quando esta questão é encontrada:

- O servidor vCenter \<vCenter> não é alcançável devido ao erro: O servidor remoto devolveu um erro: (503) Servidor Indisponível
- O servidor vCenter \<vCenter> não é alcançável devido ao erro: O servidor remoto devolveu um erro: Não é possível ligar-se ao servidor remoto.
- Não é possível ligar ao servidor vCenter/ESXi.

Para resolver o problema:

Descarregue a [ferramenta PsExec](https://aka.ms/PsExec). 

Utilize a ferramenta PsExec para aceder ao contexto do utilizador do Sistema e determinar se o endereço proxy está configurado. Em seguida, pode adicionar vCenter à lista de bypass utilizando os seguintes procedimentos.

Para a configuração de procuração discovery:

1. Abra o IE no contexto do utilizador do sistema utilizando a ferramenta PsExec.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Modifique as definições de procuração no Internet Explorer para contornar o endereço IP do vCenter.
3. Reinicie o serviço tmanssvc.

Para a configuração de procuração DRA:

1. Abra um pedido de comando e abra a pasta do Provedor de Recuperação do Site Microsoft Azure.
 
    **cd C:\Ficheiros de programa\Microsoft Azure Site Recovery Provider**

3. A partir do comando, executar o seguinte comando.
   
   **DRCONFIGURATOR.EXE /configuração /AddBypassUrls [Endereço IP/FQDN do servidor vCenter fornecido no momento do add vCenter]**

4. Reinicie o serviço de prestadores DE DRA.

## <a name="next-steps"></a>Passos seguintes

[Gerir o servidor de configuração para recuperação de desastres VMware VM](./vmware-azure-manage-configuration-server.md#refresh-configuration-server) 
