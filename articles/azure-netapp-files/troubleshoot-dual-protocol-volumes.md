---
title: Resolução de problemas de volumes de protocolo duplo para ficheiros Azure NetApp Microsoft Docs
description: Descreve mensagens de erro e resoluções que podem ajudá-lo a resolver problemas de protocolo duplo para ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: 0ae7e8f745a91e080d12a47271057ed90f9bc835
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134335"
---
# <a name="troubleshoot-dual-protocol-volumes"></a>Resolver problemas de volumes de protocolo duplo

Este artigo descreve resoluções para as condições de erro que pode ter ao criar ou gerir volumes de protocolo duplo.

## <a name="error-conditions-and-resolutions"></a>Condições e resoluções de erros

|     Condições de erro    |     Resolução    |
|-|-|
| A criação de volume de duplo protocolo falha com o erro `This Active Directory has no Server root CA Certificate` .    |     Se este erro ocorrer quando estiver a criar um volume de dois protocolos, certifique-se de que o certificado de CA raiz é carregado na sua conta NetApp.    |
| A criação de volume de duplo protocolo falha com o erro `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  O registo do ponteiro (PTR) da máquina hospedeira de AD pode estar em falta no servidor DNS. É necessário criar uma zona de procura inversa no servidor DNS e, em seguida, adicionar um registo PTR da máquina hospedeira de AD nessa zona de procura inversa. <br> Por exemplo, assuma que o endereço IP da máquina AD `1.1.1.1` é, o nome de hospedeiro da máquina AD (como se encontra através do `hostname` comando) é `AD1` , e o nome de domínio é `contoso.com` .  O registo PTR adicionado à zona de procuração inversa deve ser `1.1.1.1`  ->  `contoso.com` .   |
| A criação de volume de duplo protocolo falha com o erro `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 1.1.1.1, port 88 using TCP\\n**[ 950] FAILURE` . |  Este erro indica que a palavra-passe AD está incorreta quando o Ative Directory é associado à conta NetApp. Atualize a ligação AD com a palavra-passe correta e tente novamente. |
| A criação de volume de duplo protocolo falha com o erro `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Este erro indica que o DNS não está acessível. A razão pode ser porque o DNS IP está incorreto, ou há um problema de networking. Verifique o DNS IP introduzido na ligação AD e certifique-se de que o IP está correto. <br> Além disso, certifique-se de que o AD e o volume estão na mesma região e no mesmo VNet. Se estiverem em VNETs diferentes, certifique-se de que o espremiado VNet é estabelecido entre os dois VNets.|
| É negado o erro de permissão ao montar um volume de dois protocolos. | Um volume de duplo protocolo suporta os protocolos NFS e SMB.  Quando tenta aceder ao volume montado no sistema UNIX, o sistema tenta mapear o utilizador UNIX que utiliza para um utilizador do Windows. Se não for encontrado mapeamento, o erro "Permissão negada" ocorre. <br> Esta situação aplica-se também quando utiliza o utilizador 'raiz' para o acesso. <br> Para evitar o problema "Permissão negada", certifique-se de que o Windows Ative Directory inclui `pcuser` antes de aceder ao ponto de montagem. Se adicionar `pcuser` depois de encontrar o problema "Permissão negada", aguarde 24 horas para que a entrada do cache fique limpa antes de tentar novamente o acesso. |

## <a name="next-steps"></a>Passos seguintes  

* [Criar um volume de protocolo duplo](create-volumes-dual-protocol.md)
* [Configurar um cliente NFS para o Azure NetApp Files](configure-nfs-clients.md)
