---
title: Exibindo e modificando nomes de host | Microsoft Docs
description: Como exibir e alterar nomes de host para máquinas virtuais do Azure, funções Web e de trabalho para resolução de nomes
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: cce248e2906f4a36737388e8cc7124b1bb19fbae
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058665"
---
# <a name="viewing-and-modifying-hostnames"></a>Exibindo e modificando nomes de host
Para permitir que as instâncias de função sejam referenciadas pelo nome do host, você deve definir o valor do nome do host no arquivo de configuração de serviço para cada função. Você faz isso adicionando o nome de host desejado ao atributo **vmName** do elemento **role** . O valor do atributo **vmName** é usado como uma base para o nome de host de cada instância de função. Por exemplo, se **vmName** for *WebRole* e houver três instâncias dessa função, os nomes de host das instâncias serão *webrole0*, *WebRole1*e *webrole2*. Você não precisa especificar um nome de host para máquinas virtuais no arquivo de configuração, porque o nome do host para uma máquina virtual é preenchido com base no nome da máquina virtual. Para obter mais informações sobre como configurar um serviço de Microsoft Azure, consulte [esquema de configuração de serviço do Azure (arquivo. cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Exibindo nomes de host
Você pode exibir os nomes de host de máquinas virtuais e instâncias de função em um serviço de nuvem usando qualquer uma das ferramentas a seguir.

### <a name="service-configuration-file"></a>Arquivo de configuração de serviço
Você pode baixar o arquivo de configuração de serviço para um serviço implantado na folha **Configurar** do serviço no portal do Azure. Em seguida, você pode procurar o atributo **vmName** para o elemento de **nome de função** para ver o nome do host. Tenha em mente que esse nome de host é usado como base para o nome de host de cada instância de função. Por exemplo, se **vmName** for *WebRole* e houver três instâncias dessa função, os nomes de host das instâncias serão *webrole0*, *WebRole1*e *webrole2*.

### <a name="remote-desktop"></a>Ambiente de Trabalho Remoto
Depois de habilitar as conexões Área de Trabalho Remota (Windows), comunicação remota do Windows PowerShell (Windows) ou SSH (Linux e Windows) com suas máquinas virtuais ou instâncias de função, você pode exibir o nome do host de uma conexão do Active Área de Trabalho Remota de várias maneiras:

* Digite hostname no prompt de comando ou terminal SSH.
* Digite ipconfig/all no prompt de comando (somente Windows).
* Exiba o nome do computador nas configurações do sistema (somente Windows).

### <a name="azure-service-management-rest-api"></a>API REST de gerenciamento de serviços do Azure
Em um cliente REST, siga estas instruções:

1. Verifique se você tem um certificado de cliente para se conectar ao portal do Azure. Para obter um certificado de cliente, siga as etapas apresentadas [em como: Baixe e importe as informações](https://msdn.microsoft.com/library/dn385850.aspx)de assinatura e as configurações de publicação. 
2. Defina uma entrada de cabeçalho denominada x-MS-Version com um valor de 2013-11-01.
3. Envie uma solicitação no seguinte\/formato: https:/Management.Core.Windows.NET/\<assinatura-ID\>/Services/hostedservices/\<Service-Name\>? embed-Detail = true
4. Procure o elemento **hostname** para cada elemento **RoleInstance** .

> [!WARNING]
> Você também pode exibir o sufixo de domínio interno para seu serviço de nuvem da resposta de chamada REST, verificando o elemento **InternalDnsSuffix** ou executando ipconfig/all em um prompt de comando em uma sessão de área de trabalho remota (Windows) ou executando cat/etc/ resolução. conf de um terminal SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Modificando um nome de host
Você pode modificar o nome do host de qualquer máquina virtual ou instância de função carregando um arquivo de configuração de serviço modificado ou renomeando o computador de uma sessão de Área de Trabalho Remota.

## <a name="next-steps"></a>Passos Seguintes
[Resolução de nomes (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Esquema de configuração do serviço do Azure (. cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Esquema de configuração de rede virtual do Azure](https://go.microsoft.com/fwlink/?LinkId=248093)

[Especificar as configurações de DNS usando arquivos de configuração de rede](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

