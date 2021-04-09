---
title: Visualização e modificação de hostnames | Microsoft Docs
description: Como visualizar e alterar nomes de anfitriões para máquinas virtuais Azure, funções web e trabalhadores para resolução de nomes
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: ed250e3f32965fc450102fb14b93b93d6753ab3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222790"
---
# <a name="viewing-and-modifying-hostnames"></a>Visualização e modificação de hostnames
Para permitir que as suas instâncias de função sejam referenciadas pelo nome de anfitrião, deve definir o valor do nome de anfitrião no ficheiro de configuração de serviço para cada função. Faça isso adicionando o nome de anfitrião desejado ao atributo **vmName** do elemento **Role.** O valor do atributo **vmName** é usado como base para o nome de anfitrião de cada instância de função. Por exemplo, se **o vmName** for *webrole* e houver três instâncias desse papel, os nomes dos anfitriões dos casos serão *webrole0,* *webrole1* e *webrole2*. Não é necessário especificar um nome de anfitrião para máquinas virtuais no ficheiro de configuração, porque o nome de anfitrião de uma máquina virtual é preenchido com base no nome da máquina virtual. Para obter mais informações sobre a configuração de um serviço Microsoft Azure, consulte [o Esquema de Configuração do Serviço Azure (.cscfg File)](/previous-versions/azure/reference/ee758710(v=azure.100))

## <a name="viewing-hostnames"></a>Visualização de números de anfitrião
Pode ver os nomes dos anfitriões de máquinas virtuais e instâncias de papel num serviço de nuvem utilizando qualquer uma das ferramentas abaixo.

### <a name="service-configuration-file"></a>Ficheiro de configuração de serviço
Pode descarregar o ficheiro de configuração de serviço para um serviço implantado a partir da lâmina **configurar** do serviço no portal Azure. Em seguida, pode procurar o atributo **vmName** para o elemento **nome Role** para ver o nome do anfitrião. Tenha em mente que este nome de hospedeiro é usado como base para o nome de anfitrião de cada instância. Por exemplo, se **o vmName** for *webrole* e houver três instâncias desse papel, os nomes dos anfitriões dos casos serão *webrole0,* *webrole1* e *webrole2*.

### <a name="remote-desktop"></a>Ambiente de Trabalho Remoto
Depois de ativar as ligações de Desktop Remoto (Windows), Windows PowerShell (Windows) ou SSH (Linux e Windows) às suas máquinas virtuais ou instâncias de função, pode ver o nome de anfitrião a partir de uma ligação de ambiente de trabalho remoto ativa de várias formas:

* Digite o nome de anfitrião na solicitação de comando ou no terminal SSH.
* Digite ipconfig /tudo na indicação de comando (apenas para Windows).
* Ver o nome do computador nas definições do sistema (apenas Windows).

### <a name="azure-service-management-rest-api"></a>Azure Service Management REST API
A partir de um cliente REST, siga estas instruções:

1. Certifique-se de que tem um certificado de cliente para ligar ao portal Azure. Para obter um certificado de cliente, siga os passos apresentados em [Como: Baixar e Importar Configurações de Publicação e Informações de Subscrição.](/previous-versions/dynamicsnav-2013/dn385850(v=nav.70)) 
2. Desa estação uma entrada de cabeçalho denominada versão x-ms com um valor de 2013-11-01.
3. Enviar um pedido no seguinte formato: https: \/ https: /management.core.windows.net/ \<subscrition-id\> /services/hostedservices/ \<service-name\> ?embed-detail=true
4. Procure o elemento **HostName** para cada elemento **RoleInstance.**

> [!WARNING]
> Também pode ver o sufixo de domínio interno para o seu serviço de nuvem a partir da resposta de chamada REST, verificando o elemento **InternalDnsSuffix,** ou executando ipconfig /todos a partir de uma solicitação de comando numa sessão de ambiente de trabalho remoto (Windows), ou executando gato /etc/resolv.conf a partir de um terminal SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Modificar um nome de anfitrião
Pode modificar o nome de anfitrião para qualquer máquina virtual ou instância de função carregando um ficheiro de configuração de serviço modificado ou renomeando o computador a partir de uma sessão de Ambiente de Trabalho Remoto.

## <a name="next-steps"></a>Passos seguintes
[Resolução de nomes (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Esquema de Configuração de Serviço Azure (.cscfg)](/previous-versions/azure/reference/ee758710(v=azure.100))

[Esquema de configuração de rede virtual Azure](/previous-versions/azure/reference/jj157100(v=azure.100))

[Especifique as definições de DNS utilizando ficheiros de configuração de rede](/previous-versions/azure/virtual-network/virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file)