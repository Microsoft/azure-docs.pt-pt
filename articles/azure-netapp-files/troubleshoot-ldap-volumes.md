---
title: Problemas de volume LDAP para ficheiros Azure NetApp | Microsoft Docs
description: Descreve resoluções para condições de erro que poderá ter ao configurar volumes LDAP para ficheiros Azure NetApp.
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
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: eea3f691bc6d91948dc73b4a02c89abfac12d384
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106499030"
---
# <a name="troubleshoot-ldap-volume-issues"></a>Problemas de volume de LDAP resolução de problemas

Este artigo descreve resoluções para as condições de erro que pode ter ao configurar volumes LDAP.

## <a name="errors-and-resolutions-for-ldap-volumes"></a>Erros e resoluções para volumes LDAP

|     Condições de erro    |     Resoluções    |
|-|-|
| Erro ao criar um volume SMB com ldapEnabled como verdadeiro: <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | Não é possível criar um volume SMB com LDAP ativado. <br> Criar volumes SMB com LDAP desativado. |
| Erro ao atualizar o valor do parâmetro ldapEnabled para um volume existente: <br> `Error Message: ldapEnabled parameter is not allowed to update` |  Não é possível modificar a definição de opção LDAP após a criação de um volume. <br> Não atualize a definição de opção LDAP num volume criado. Consulte [o Configure ADDS LDAP com grupos alargados para acesso ao volume NFS](configure-ldap-extended-groups.md) para mais detalhes. |
| Erro ao criar um volume NFS ativado por LDAP: <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  Este erro ocorre porque o DNS é inacessível. <br> <ul><li> Verifique se configura o site correto (deteção de site) para ficheiros Azure NetApp. </li><li> A razão pela qual o DNS é inacessível pode ser um endereço IP DNS incorreto ou problemas de rede. Verifique o endereço DNS IP introduzido na ligação AD para se certificar de que está correto. </li><li> Certifique-se de que o AD e o volume estão na mesma região e no mesmo VNet. Se estiverem em VNets diferentes, certifique-se de que o espremiado VNet é estabelecido entre os dois VNets.</li></ul> |
| Erro ao criar volume a partir de uma imagem instantânea: <br> `Aggregate does not exist` | O Azure NetApp Files não suporta o fornecimento de um novo volume, ativado por LDAP, a partir de um instantâneo que pertence a um volume com incapacitação LDAP. <br> Tente criar um novo volume com incapacitação LDAP a partir do instantâneo dado. |

## <a name="next-steps"></a>Passos seguintes  

* [Configure ADDS LDAP com grupos alargados para acesso ao volume NFS](configure-ldap-extended-groups.md)
* [Criar um volume NFS para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Criar um volume de duplo protocolo (NFSv3 e SMB) para ficheiros Azure NetApp](create-volumes-dual-protocol.md)