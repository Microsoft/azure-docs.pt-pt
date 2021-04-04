---
title: Problemas de resolução de problemas NFSv4.1 Problemas de volume Kerberos para ficheiros Azure NetApp | Microsoft Docs
description: Descreve mensagens de erro e resoluções que podem ajudá-lo a resolver problemas problemas de problemas de volume NFSv4.1 Kerberos para ficheiros Azure NetApp.
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
ms.openlocfilehash: 638607da02b1db4842cdc04f86a4fed1860c243f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98134318"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>Problemas de resolução NFSv4.1 Problemas de volume Kerberos 

Este artigo descreve resoluções para as condições de erro que pode ter ao criar ou gerir volumes NFSv4.1 Kerberos. 

## <a name="error-conditions-and-resolutions"></a>Condições e resoluções de erros

|     Condições de erro    |     Resoluções    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | O Azure NetApp Files não suporta Kerberos para volumes NFSv3. Kerberos é apoiado apenas para o protocolo NFSv4.1.  |
|`This NetApp account has no configured Active Directory   connections`  |  Configurar o Diretório Ativo para a conta NetApp com os campos **KDC IP** e **AD Server Name**. Consulte [o Portal Azure](configure-kerberos-encryption.md#configure-the-azure-portal) para obter instruções. |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | O Azure NetApp Files não suporta a conversão de um volume NFSv4.1 simples para o volume Kerberos NFSv4.1, e vice-versa. |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  Exemplo: `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> Certifique-se de que os registos A/PTR estão corretamente configurado e existem no Diretório Ativo para o nome do servidor `smb-test-64d9.contoso.com` . <br> No cliente NFS, se `nslookup` de resolver o endereço `smb-test-64d9.contoso.com` IP1 (isto é, `10.1.1.68` ), então o `nslookup` IP1 deve resolver apenas um registo (isto é, `smb-test-64d9.contoso.com` ). `nslookup` do IP1 não *deve* resolver vários nomes. </li>  <li>Desa estade o AES-256 para a conta de máquina NFS do tipo `NFS-<Smb NETBIOS NAME>-<few random characters>` em AD utilizando o PowerShell ou o UI. <br> Comandos de exemplo: <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>Certifique-se de que o tempo do software de armazenamento do cliente NFS, AD e Azure NetApp Files está sincronizado entre si e está dentro de um intervalo de cinco minutos. </li>  <li>Obtenha o bilhete Kerberos no cliente da NFS usando o `kinit <administrator>` comando.</li> <li>Reduza o nome de anfitrião do cliente NFS para menos de 15 caracteres e execute o reino juntando-se novamente. </li><li>Reinicie o cliente NFS e o `rpcgssd` serviço da seguinte forma. O comando pode variar dependendo do SO.<br> RHEL 7: <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8: <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu: <br> (Reiniciar o `rpc-gssd` serviço.) <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | A questão pode estar relacionada com a questão do cliente da NFS. Reinicie o cliente da NFS.    |
|`Hostname lookup failed`   | É necessário criar uma zona de procura inversa no servidor DNS e, em seguida, adicionar um registo PTR da máquina hospedeira de AD nessa zona de procura inversa. <br> Por exemplo, assuma que o endereço IP da máquina AD `10.1.1.4` é, o nome de anfitrião da máquina AD (como se encontra através do comando do nome de anfitrião) é `AD1` , e o nome de domínio é `contoso.com` . O registo PTR adicionado à zona de procuração inversa deve ser `10.1.1.4 -> AD1.contoso.com` . |
|`Volume creation fails due to unreachable DNS server`  | Estão disponíveis duas soluções possíveis: <br> <ul><li> Este erro indica que o DNS não está acessível. A razão pode ser um IP DNS incorreto ou um problema de networking. Verifique o DNS IP introduzido na ligação AD e certifique-se de que o IP está correto. </li> <li> Certifique-se de que o AD e o volume estão na mesma região e no mesmo VNet. Se estiverem em VNets diferentes, certifique-se de que o espremiado VNet é estabelecido entre os dois VNets. </li></ul> |
|A criação de volume kerberos falha com um erro semelhante ao seguinte exemplo: <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |O IP KDC está errado e o volume Kerberos foi criado. Atualize o IP do KDC com um endereço correto. <br> Depois de atualizar o IP do KDC, o erro não desaparecerá. Tens de recriar o volume. |

## <a name="next-steps"></a>Passos seguintes  

* [Configurar a encriptação NFSv4.1 Kerberos para o Azure NetApp Files](configure-kerberos-encryption.md)
