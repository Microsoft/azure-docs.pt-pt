---
title: Crie um volume de duplo protocolo (NFSv3 e SMB) para ficheiros Azure NetApp | Microsoft Docs
description: Descreve como criar um volume que utiliza o protocolo duplo de NFSv3 e SMB com suporte para mapeamento de utilizador LDAP.
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
ms.openlocfilehash: b6a2d7ad92c209a93d740d60808c2cbd2f90c6b4
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258423"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Criar um volume de duplo protocolo (NFSv3 e SMB) para ficheiros Azure NetApp

O Azure NetApp Files suporta a criação de volumes utilizando NFSv3 (NFSv3 e NFSv4.1), SMB3 ou protocolo duplo. Este artigo mostra-lhe como criar um volume que utiliza o protocolo duplo de NFSv3 e SMB com suporte para mapeamento de utilizador LDAP. 

Para criar volumes NFS, consulte [Criar um volume NFS](azure-netapp-files-create-volumes.md). Para criar volumes SMB, consulte [Criar um volume SMB](azure-netapp-files-create-volumes-smb.md). 

## <a name="before-you-begin"></a>Antes de começar 

* Você já deve ter criado uma piscina de capacidade.  
    Consulte [configurar uma piscina de capacidade.](azure-netapp-files-set-up-capacity-pool.md)   
* Uma sub-rede deve ser delegada nos Ficheiros Azure NetApp.  
    Consulte [a sub-rede de delegados aos ficheiros Azure NetApp](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Considerações

* Certifique-se de que cumpre os [requisitos para ligações de Diretório Ativo](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* Crie uma zona de procura inversa no servidor DNS e, em seguida, adicione um registo de ponteiro (PTR) da máquina hospedeira de AD nessa zona de procura inversa. Caso contrário, a criação de volume de duplo protocolo falhará.
* Confirme que o cliente NFS está atualizado e a executar as atualizações mais recentes do sistema operativo.
* Certifique-se de que o servidor LDAP do Ative Directory (AD) está a funcionar no AD. Pode fazê-lo instalando e configurando o papel [de Diretório Leve Ativo (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) na máquina AD.
* Atualmente, os volumes de protocolo duplo não suportam os Serviços de Domínio do Diretório Ativo Azure (AADDS). O LDAP sobre o TLS não deve ser ativado se estiver a utilizar o AADDS.
* A versão NFS utilizada por um volume de duplo protocolo é NFSv3. Como tal, aplicam-se as seguintes considerações:
    * O protocolo dual não suporta os atributos estendidos do Windows ACLS `set/get` dos clientes NFS.
    * Os clientes NFS não podem alterar permissões para o estilo de segurança NTFS, e os clientes windows não podem alterar permissões para volumes de protocolo duplo estilo UNIX.   

    A tabela que se segue descreve os estilos de segurança e os seus efeitos:  
    
    | Estilo de segurança    | Clientes que podem modificar permissões   | Permissões que os clientes podem usar  | Consequente estilo de segurança eficaz    | Clientes que podem aceder a ficheiros     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | Bits de modo NFSv3   | UNIX  | NFS e Windows   |
    | `Ntfs`    | Windows   | NTFS ACLs     | NTFS  |NFS e Windows|
* Os utilizadores unix que montam o volume de estilo de segurança NTFS utilizando o NFS serão autenticados como utilizador do Windows `root` para o UNIX `root` e para todos `pcuser` os outros utilizadores. Certifique-se de que estas contas de utilizador existem no seu Ative Directory antes de montar o volume quando utilizar o NFS. 
* Se tiver grandes topologias e utilizar o `Unix` estilo de segurança com um volume de dois protocolos ou LDAP com grupos alargados, o Azure NetApp Files pode não conseguir aceder a todos os servidores nas suas topologias.  Se esta situação ocorrer, contacte a sua equipa de conta para obter assistência.  <!-- NFSAAS-15123 --> 
* Não precisa de um certificado de CA raiz de servidor para criar um volume de protocolo duplo. Só é necessário se o LDAP sobre o TLS estiver ativado.


## <a name="create-a-dual-protocol-volume"></a>Criar um volume de protocolo duplo

1.  Clique na lâmina **volumes** da lâmina Pools de Capacidade. Clique em **+ Adicionar volume** para criar um volume. 

    ![Navegar para volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Na janela Criar um Volume, clique em **Criar** e fornecer informações para os seguintes campos no separador Básicos:   
    * **Nome do volume**      
        Especifique o nome do volume que está a criar.   

        Um nome de volume deve ser único dentro de cada piscina de capacidade. Deve ter pelo menos três caracteres de comprimento. Pode usar qualquer caracteres alfanuméricos.   

        Não pode usar `default` ou como nome de `bin` volume.

    * **Piscina de capacidade**  
        Especifique o pool de capacidade onde deseja que o volume seja criado.

    * **Quota**  
        Especifique a quantidade de armazenamento lógico que está atribuída ao volume.  

        O campo **Quota disponível** mostra a quantidade de espaço não utilizado no conjunto de capacidade escolhido que pode usar para criar um novo volume. O tamanho do novo volume não pode exceder a quota disponível.  

    * **Produção (MiB/S)**   
        Se o volume for criado num pool manual de capacidade QoS, especifique a produção desejada para o volume.   

        Se o volume for criado num pool de capacidade auto QoS, o valor apresentado neste campo é (rendimento de nível de serviço quota x).   

    * **Rede virtual**  
        Especifique a rede virtual Azure (VNet) a partir da qual pretende aceder ao volume.  

        O Vnet que especifica deve ter uma sub-rede delegada nos Ficheiros Azure NetApp. O serviço Azure NetApp Files só pode ser acedido a partir do mesmo Vnet ou de um Vnet que está na mesma região que o volume através do espreitamento Vnet. Também pode aceder ao volume a partir da sua rede no local através da Rota Expresso.   

    * **Sub-rede**  
        Especifique a sub-rede que pretende utilizar para o volume.  
        A sub-rede que especificou deve ser delegada nos Ficheiros Azure NetApp. 
        
        Se não tiver delegado uma sub-rede, pode clicar em **Criar uma** página de Volume. Em seguida, na página 'Criar Sub-redes', especificar as informações da sub-rede e selecionar **o Microsoft.NetApp/volumes** para delegar a sub-rede para ficheiros Azure NetApp. Em cada Vnet, apenas uma sub-rede pode ser delegada nos Ficheiros Azure NetApp.   
 
        ![Criar um volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Criar sub-rede](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Se pretender aplicar uma política de instantâneo existente no volume, clique em **Mostrar secção avançada** para expandi-la, especificar se pretende ocultar o caminho do instantâneo e selecionar uma política de instantâneo no menu pull-down. 

        Para obter informações sobre a criação de uma política de instantâneo, consulte [Gerir as políticas de instantâneo](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Mostrar seleção avançada](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Clique **em Protocolo** e, em seguida, complete as seguintes ações:  
    * Selecione **o protocolo duplo (NFSv3 e SMB)** como o tipo de protocolo para o volume.   

    * Especifique o **percurso de volume** para o volume.   
    Este percurso de volume é o nome do volume partilhado. O nome deve começar com um carácter alfabético, e deve ser único em cada subscrição e em cada região.  

    * Especifique o **Estilo de Segurança** para utilizar: NTFS (padrão) ou UNIX.

    * Opcionalmente, [configurar a política de exportação para o volume](azure-netapp-files-configure-export-policy.md).

    ![Especificar protocolo duplo](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Clique **em 'Rever + Criar'** para rever os detalhes do volume. Em seguida, clique em **Criar** para criar o volume.

    O volume que criou aparece na página Volumes. 
 
    Um volume herda a subscrição, grupo de recursos e atributos de localização do seu conjunto de capacidade. Para monitorizar o estado da implementação do volume, pode utilizar o separador Notificações.

## <a name="allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume"></a>Permitir que os utilizadores locais de NFS com LDAP acedam a um volume de duplo protocolo 

Pode permitir que os utilizadores locais de clientes NFS não presentes no servidor Do Windows LDAP acedam a um volume de dois protocolos que tenha LDAP com grupos alargados ativados. Para tal, ative os **utilizadores locais de NFS com** a opção LDAP da seguinte forma:

1. Clique nas **ligações ative directory**.  Numa ligação ative de diretório existente, clique no menu de contexto (os três `…` pontos) e **selecione Editar**.  

2. Na janela **de definições de configuração do Diretório Ativo de Edição** que aparece, selecione os **utilizadores locais de NFS com** a opção LDAP.  

    ![Screenshot que mostra o permitir utilizadores locais de NFS com opção LDAP](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  


## <a name="manage-ldap-posix-attributes"></a>Gerir atributos LDAP POSIX

Pode gerir atributos POSIX como UID, Home Directory e outros valores utilizando o snap-in do Ative Directory Users e computers MMC.  O exemplo a seguir mostra o Editor de Atributos do Diretório Ativo:  

![Editor de Atributos de Diretório Ativo](../media/azure-netapp-files/active-directory-attribute-editor.png) 

Tem de definir os seguintes atributos para utilizadores LDAP e grupos LDAP: 
* Atributos necessários para utilizadores de LDAP:   
    `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: posixAccount`
* Atributos necessários para grupos LDAP:   
    `objectClass: posixGroup`, `gidNumber: 555`

## <a name="configure-the-nfs-client"></a>Configure o cliente NFS 

Siga as instruções em [Configurar um cliente NFS para a Azure NetApp Files](configure-nfs-clients.md) para configurar o cliente NFS.  

## <a name="next-steps"></a>Passos seguintes  

* [Configurar um cliente NFS para o Azure NetApp Files](configure-nfs-clients.md)
* [SMB de resolução de problemas ou volumes de duplo protocolo](troubleshoot-dual-protocol-volumes.md)
* [Problemas de volume de LDAP resolução de problemas](troubleshoot-ldap-volumes.md)
