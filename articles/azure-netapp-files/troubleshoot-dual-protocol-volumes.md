---
title: Resolução de problemas volumes SMB ou duplo-protocolo para ficheiros Azure NetApp | Microsoft Docs
description: Descreve mensagens de erro e resoluções que podem ajudá-lo a resolver problemas de SMB ou problemas de protocolo duplo para ficheiros Azure NetApp.
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
ms.date: 02/02/2021
ms.author: b-juche
ms.openlocfilehash: dbc9f466437a575866c33219ff11af2d85d9a58b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571081"
---
# <a name="troubleshoot-smb-or-dual-protocol-volumes"></a>SMB de resolução de problemas ou volumes de duplo protocolo

Este artigo descreve resoluções para as condições de erro que pode ter ao criar ou gerir volumes de protocolo duplo.

## <a name="errors-for-dual-protocol-volumes"></a>Erros para volumes de duplo protocolo

|     Condições de erro    |     Resoluções    |
|-|-|
| O LDAP sobre tLS está ativado e a criação de volume de duplo protocolo falha com o erro `This Active Directory has no Server root CA Certificate` .    |     Se este erro ocorrer quando estiver a criar um volume de dois protocolos, certifique-se de que o certificado de CA raiz é carregado na sua conta NetApp.    |
| A criação de volume de duplo protocolo falha com o erro `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  O registo do ponteiro (PTR) da máquina hospedeira de AD pode estar em falta no servidor DNS. É necessário criar uma zona de procura inversa no servidor DNS e, em seguida, adicionar um registo PTR da máquina hospedeira de AD nessa zona de procura inversa. <br> Por exemplo, assuma que o endereço IP da máquina AD `10.X.X.X` é, o nome de hospedeiro da máquina AD (como se encontra através do `hostname` comando) é `AD1` , e o nome de domínio é `contoso.com` .  O registo PTR adicionado à zona de procuração inversa deve ser `10.X.X.X`  ->  `contoso.com` .   |
| A criação de volume de duplo protocolo falha com o erro `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 10.X.X.X, port 88 using TCP\\n**[ 950] FAILURE` . |     Este erro indica que a palavra-passe AD está incorreta quando o Ative Directory é associado à conta NetApp. Atualize a ligação AD com a palavra-passe correta e tente novamente. |
| A criação de volume de duplo protocolo falha com o erro `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Este erro indica que o DNS não está acessível. A razão pode ser porque o DNS IP está incorreto, ou há um problema de networking. Verifique o DNS IP introduzido na ligação AD e certifique-se de que o IP está correto. <br> Além disso, certifique-se de que o AD e o volume estão na mesma região e no mesmo VNet. Se estiverem em VNETs diferentes, certifique-se de que o espremiado VNet é estabelecido entre os dois VNets.|
| É negado o erro de permissão ao montar um volume de dois protocolos. | Um volume de duplo protocolo suporta os protocolos NFS e SMB.  Quando tenta aceder ao volume montado no sistema UNIX, o sistema tenta mapear o utilizador UNIX que utiliza para um utilizador do Windows. Se não for encontrado mapeamento, o erro "Permissão negada" ocorre. <br> Esta situação aplica-se também quando utiliza o utilizador 'raiz' para o acesso. <br> Para evitar o problema "Permissão negada", certifique-se de que o Windows Ative Directory inclui `pcuser` antes de aceder ao ponto de montagem. Se adicionar `pcuser` depois de encontrar o problema "Permissão negada", aguarde 24 horas para que a entrada do cache fique limpa antes de tentar novamente o acesso. |

## <a name="common-errors-for-smb-and-dual-protocol-volumes"></a>Erros comuns para volumes de SMB e duplo protocolo

|     Condições de erro    |     Resoluções    |
|-|-|
| A criação de volume SMB ou dual-protocol falha com o seguinte erro: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available."}]}` | Este erro indica que o DNS não está acessível. <br> Considere as seguintes soluções: <ul><li>Verifique se os ADDS e o volume estão a ser implantados na mesma região.</li> <li>Verifique se o ADDS e o volume estão a utilizar o mesmo VNet. Se estiverem a utilizar VNETs diferentes, certifique-se de que os VNets são espreitados entre si. Consulte as Diretrizes para o planeamento da [rede de ficheiros Azure NetApp](azure-netapp-files-network-topologies.md). </li> <li>O servidor DNS pode ter grupos de segurança de rede (NSGs) aplicados.  Como tal, não permite que o tráfego flua. Neste caso, abra os NSGs ao DNS ou AD para ligar a várias portas. Para os requisitos portuários, consulte [requisitos para ligações de Diretório Ativo](create-active-directory-connections.md#requirements-for-active-directory-connections). </li></ul> <br>As mesmas soluções aplicam-se ao Azure ADDS. O Azure ADDS deve ser implantado na mesma região. O VNet deve estar na mesma região ou espreitar com o VNet utilizado pelo volume. | 
| A criação de volume SMB ou dual-protocol falha com o seguinte erro: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-C1C8\". Reason: Kerberos Error: Invalid credentials were given Details: Error: Machine account creation procedure failed\n [ 563] Loaded the preliminary configuration.\n**[ 670] FAILURE: Could not authenticate as 'test@contoso.com':\n** Unknown user (KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN)\n. "}]}`  |  <ul><li>Certifique-se de que o nome de utilizador introduzido está correto. </li> <li>Certifique-se de que o utilizador faz parte do grupo Administrador que tem o privilégio de criar contas de máquinas. </li> <li> Se utilizar Azure ADDS, certifique-se de que o utilizador faz parte do grupo AD Azure `Azure AD DC Administrators` . </li></ul> | 
| A criação de volume SMB ou dual-protocol falha com o seguinte erro: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-A452\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\n [ 567] Loaded the preliminary configuration.\n [ 671] Successfully connected to ip 10.X.X.X, port 88 using TCP\n**[ 1099] FAILURE: Could not authenticate as\n** 'user@contoso.com': CIFS server account password does\n** not match password stored in Active Directory\n** (KRB5KDC_ERR_PREAUTH_FAILED)\n. "}]}` | Certifique-se de que a palavra-passe inserida para a junção da ligação AD está correta. |
| A criação de volume SMB ou dual-protocol falha com o seguinte erro: `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError","message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-D9A2\". Reason: SecD Error: ou not found Details: Error: Machine account creation procedure failed\n [ 561] Loaded the preliminary configuration.\n [ 665] Successfully connected to ip 10.X.X.X, port 88 using TCP\n [ 1039] Successfully connected to ip 10.x.x.x, port 389 using TCP\n**[ 1147] FAILURE: Specifed OU 'OU=AADDC Com' does not exist in\n** contoso.com\n. "}]}` | Certifique-se de que o caminho da UO especificado para a junção da ligação AD está correto. Se utilizar o Azure ADDS, certifique-se de que o percurso da unidade organizacional é `OU=AADDC Computers` . |

## <a name="next-steps"></a>Passos seguintes  

* [Criar um volume SMB](azure-netapp-files-create-volumes-smb.md)
* [Criar um volume de protocolo duplo](create-volumes-dual-protocol.md)
* [Configurar um cliente NFS para o Azure NetApp Files](configure-nfs-clients.md)
