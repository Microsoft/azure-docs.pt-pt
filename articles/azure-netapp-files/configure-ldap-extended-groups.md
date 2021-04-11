---
title: Configure ADDS LDAP com grupos alargados para o acesso ao volume NFS dos Ficheiros NFS do Azure NetApp | Microsoft Docs
description: Descreve as considerações e passos para permitir lDAP com grupos estendidos quando cria um volume NFS utilizando ficheiros Azure NetApp.
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
ms.topic: how-to
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: 2031cbf07d700307ae1e11c516f9fc736bce5080
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106499017"
---
# <a name="configure-adds-ldap-with-extended-groups-for-nfs-volume-access"></a>Configure ADDS LDAP com grupos alargados para acesso ao volume NFS

Quando [cria um volume NFS,](azure-netapp-files-create-volumes.md)tem a opção de ativar o LDAP com uma função de grupos alargados (a opção **LDAP)** para o volume. Esta funcionalidade permite que utilizadores do Diretório Ativo LDAP e grupos alargados (até 1024 grupos) acedam ao volume.  

Este artigo explica as considerações e passos para permitir lDAP com grupos estendidos quando cria um volume NFS.  

## <a name="considerations"></a>Considerações

* O LDAP sobre o TLS *não* deve ser ativado se estiver a utilizar os Serviços de Domínio do Diretório Ativo Azure (AADDS).  

* Se ativar o LDAP com uma funcionalidade de grupos [alargados, os volumes Kerberos](configure-kerberos-encryption.md) ativados pelo LDAP não mostrarão corretamente a propriedade do ficheiro para utilizadores LDAP. Um ficheiro ou diretório criado por um utilizador LDAP irá por defeito `root` como proprietário em vez do utilizador LDAP real. No entanto, a `root` conta pode alterar manualmente a propriedade do ficheiro utilizando o comando `chown <username> <filename>` . 

* Não é possível modificar a definição de opção LDAP (ativada ou desativada) depois de ter criado o volume.  

* A tabela seguinte descreve as definições De Tempo a Viver (TTL) para a cache LDAP. Tem de esperar até que a cache seja renovada antes de tentar aceder a um ficheiro ou diretório através de um cliente. Caso contrário, uma mensagem de acesso negada aparece no cliente. 

    |     Condição de erro    |     Resolução    |
    |-|-|
    | Cache |  Tempo Limite Predefinido |
    | Lista de membros do grupo  | TTL 24 horas  |
    | Grupos Unix  | TTL 24 horas, TTL negativo de 1 minuto  |
    | Utilizadores unix  | TTL 24 horas, TTL negativo de 1 minuto  |

    Caches têm um período de tempo específico chamado *Time to Live*. Após o período de tempo limite, as entradas envelhecem para que as entradas fracas não se prolonguem. O valor *negativo da TTL* é onde uma procura que falhou reside para ajudar a evitar problemas de desempenho devido a consultas de LDAP para objetos que podem não existir."        

## <a name="steps"></a>Passos

1. O LDAP com recurso de grupos alargados está atualmente em pré-visualização. Antes de utilizar esta funcionalidade pela primeira vez, tem de registar a funcionalidade:  

    1. Registar a função:   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```

    2. Verifique o estado do registo da funcionalidade: 

        > [!NOTE]
        > O **Estado de Registo** pode estar no estado até `Registering` 60 minutos antes de mudar para `Registered` . Aguarde até que o estado seja `Registered` antes de continuar.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```
        
    Também pode utilizar [comandos Azure CLI](/cli/azure/feature) `az feature register` e registar a funcionalidade e exibir o estado de `az feature show` registo. 

2. Os volumes LDAP requerem uma configuração de Diretório Ativo para as definições do servidor LDAP. Siga as instruções nos [Requisitos de Ligações de Diretório Ativo](create-active-directory-connections.md#requirements-for-active-directory-connections) e [Crie uma ligação ative de diretório](create-active-directory-connections.md#create-an-active-directory-connection) para configurar ligações de Diretório Ativo no portal Azure.  

3. Certifique-se de que o servidor LDAP do Diretório Ativo está a funcionar no Ative Directory. Pode fazê-lo instalando e configurando o papel [de Diretório Leve Ativo (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) na máquina AD.

4. Os utilizadores do LDAP NFS precisam de ter certos atributos POSIX no servidor LDAP. Siga [gerir os atributos POSIX da LDAP](create-volumes-dual-protocol.md#manage-ldap-posix-attributes) para definir os atributos necessários.  

5. Se quiser configurar um cliente Linux integrado no LDAP, consulte [configurar um cliente NFS para ficheiros Azure NetApp](configure-nfs-clients.md).

6.  Siga os passos na [Criação de um volume NFS para ficheiros Azure NetApp](azure-netapp-files-create-volumes.md) para criar um volume NFS. Durante o processo de criação de volume, no âmbito do **separador Protocolo,** ative a opção **LDAP.**   

    ![Screenshot que mostra Criar uma página de volume com opção LDAP.](../media/azure-netapp-files/create-nfs-ldap.png)  

7. Opcional - Pode permitir que os utilizadores locais de clientes NFS não presentes no servidor Windows LDAP acedam a um volume NFS que tenha LDAP com grupos alargados ativados. Para tal, ative os **utilizadores locais de NFS com** a opção LDAP da seguinte forma:
    1. Clique nas **ligações ative directory**.  Numa ligação ative de diretório existente, clique no menu de contexto (os três `…` pontos) e **selecione Editar**.  
    2. Na janela **de definições de configuração do Diretório Ativo de Edição** que aparece, selecione os **utilizadores locais de NFS com** a opção LDAP.  

    ![Screenshot que mostra o permitir utilizadores locais de NFS com opção LDAP](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  

## <a name="next-steps"></a>Passos seguintes  

* [Criar um volume NFS para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Criar e gerir ligações ative directy](create-active-directory-connections.md)
* [Problemas de volume de LDAP resolução de problemas](troubleshoot-ldap-volumes.md)
