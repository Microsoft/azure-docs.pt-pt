---
title: Criar um volume de duplo protocolo (NFSv3 e SMB) para ficheiros Azure NetApp Microsoft Docs
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
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: ee5e1230acd059d69648144b84a8fbfa652ef6e7
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854058"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Criar um volume de duplo protocolo (NFSv3 e SMB) para ficheiros Azure NetApp

O Azure NetApp Files suporta a criação de volumes utilizando NFSv3 (NFSv3 e NFSv4.1), SMB3 ou protocolo duplo. Este artigo mostra-lhe como criar um volume que utiliza o protocolo duplo de NFSv3 e SMB com suporte para mapeamento de utilizador LDAP.  


## <a name="before-you-begin"></a>Before you begin 

* Você já deve ter criado uma piscina de capacidade.  
    Consulte [configurar uma piscina de capacidade.](azure-netapp-files-set-up-capacity-pool.md)   
* Uma sub-rede deve ser delegada nos Ficheiros Azure NetApp.  
    Consulte [a sub-rede de delegados aos ficheiros Azure NetApp](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Considerações

* Certifique-se de que cumpre os [requisitos para ligações de Diretório Ativo](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* Crie uma zona de procura inversa no servidor DNS e, em seguida, adicione um registo de ponteiro (PTR) da máquina hospedeira de AD nessa zona de procura inversa. Caso contrário, a criação de volume de duplo protocolo falhará.
* Confirme que o cliente NFS está atualizado e a executar as atualizações mais recentes do sistema operativo.
* Certifique-se de que o servidor LDAP do Ative Directory (AD) está a funcionar no AD. Pode fazê-lo instalando e configurando o papel [de Diretório Leve Ativo (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) na máquina AD.
* Certifique-se de que uma autoridade de certificados (CA) é criada na AD utilizando a função [de Serviços de Certificados de Diretório Ativo (CS)](/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) para gerar e exportar o certificado de CA de raiz auto-assinado.   
* Atualmente, os volumes de protocolo duplo não suportam os Serviços de Domínio do Diretório Ativo Azure (AADDS).  
* A versão NFS utilizada por um volume de duplo protocolo é NFSv3. Como tal, aplicam-se as seguintes considerações:
    * O protocolo dual não suporta os atributos estendidos do Windows ACLS `set/get` dos clientes NFS.
    * Os clientes NFS não podem alterar permissões para o estilo de segurança NTFS, e os clientes windows não podem alterar permissões para volumes de protocolo duplo estilo UNIX.   

    A tabela que se segue descreve os estilos de segurança e os seus efeitos:  
    
    | Estilo de segurança    | Clientes que podem modificar permissões   | Permissões que os clientes podem usar  | Consequente estilo de segurança eficaz    | Clientes que podem aceder a ficheiros     |
    |-  |-  |-  |-  |-  |
    | UNIX  | NFS   | Bits de modo NFSv3   | UNIX  | NFS e Windows   |
    | NTFS  | Windows   | NTFS ACLs     | NTFS  |NFS e Windows|
* Os utilizadores unix que montam o volume de estilo de segurança NTFS utilizando o NFS serão autenticados como utilizador do Windows `root` para o UNIX `root` e para todos `pcuser` os outros utilizadores. Certifique-se de que estas contas de utilizador existem no seu Ative Directory antes de montar o volume quando utilizar o NFS. 


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

    * Selecione a ligação **Ative Directory** da lista de drop-down.  
    O Ative Directory que utiliza deve ter um certificado de CA raiz de servidor. 

    * Especifique o **percurso de volume** para o volume.   
    Este percurso de volume é o nome do volume partilhado. O nome deve começar com um carácter alfabético, e deve ser único em cada subscrição e em cada região.  

    * Especifique o **Estilo de Segurança** para utilizar: NTFS (padrão) ou UNIX.

    * Opcionalmente, [configurar a política de exportação para o volume](azure-netapp-files-configure-export-policy.md).

    ![Especificar protocolo duplo](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Clique **em 'Rever + Criar'** para rever os detalhes do volume. Em seguida, clique em **Criar** para criar o volume.

    O volume que criou aparece na página Volumes. 
 
    Um volume herda a subscrição, grupo de recursos e atributos de localização do seu conjunto de capacidade. Para monitorizar o estado da implementação do volume, pode utilizar o separador Notificações.

## <a name="upload-active-directory-certificate-authority-public-root-certificate"></a>Upload Ative Directory Certificate Authority certificado de raiz pública  

1.  Siga [instalar a Autoridade de Certificação](/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) para instalar e configurar a Autoridade de Certificados ADDS. 

2.  Siga [os certificados de visualização com o snap-in MMC](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) para utilizar o snap-in MMC e a ferramenta Certificate Manager.  
    Utilize o snap-in do Certificate Manager para localizar a raiz ou o certificado de emissão para o dispositivo local. Deverá executar os comandos de snap-in da Gestão de Certificados a partir de uma das seguintes definições:  
    * Um cliente baseado no Windows que se juntou ao domínio e tem o certificado raiz instalado 
    * Outra máquina no domínio que contém o certificado raiz  

3. Exporte o certificado de raiz.  
    Certifique-se de que o certificado é exportado na Base-64 codificada X.509 (. Formato CER) : 

    ![Assistente de Exportação de Certificados](../media/azure-netapp-files/certificate-export-wizard.png)

4. Aceda à conta NetApp do volume de duplo protocolo, clique nas **ligações ative directory** e carre faça o upload do certificado de CA raiz utilizando a janela **'Join Ative Directory':**  

    ![Certificado de CA raiz de servidor](../media/azure-netapp-files/server-root-ca-certificate.png)

    Certifique-se de que o nome da autoridade do certificado pode ser resolvido pelo DNS. Este nome é o campo "Emitido por" ou "Emitente" no certificado:  

    ![Informações sobre certificados](../media/azure-netapp-files/certificate-information.png)

## <a name="manage-ldap-posix-attributes"></a>Gerir atributos LDAP POSIX

Pode gerir atributos POSIX como UID, Home Directory e outros valores utilizando o snap-in do Ative Directory Users e computers MMC.  O exemplo a seguir mostra o Editor de Atributos do Diretório Ativo:  

![Editor de Atributos de Diretório Ativo](../media/azure-netapp-files/active-directory-attribute-editor.png) 

Tem de definir os seguintes atributos para utilizadores LDAP e grupos LDAP: 
* Atributos necessários para utilizadores de LDAP:   
    `uid`: `uidNumber` Alice, : 139, `gidNumber` : 555, `objectClass` : posixAccount
* Atributos necessários para grupos LDAP:   
    `objectClass`: "PosixGroup", `gidNumber` : 555

## <a name="configure-the-nfs-client"></a>Configure o cliente NFS 

Siga as instruções em [Configurar um cliente NFS para a Azure NetApp Files](configure-nfs-clients.md) para configurar o cliente NFS.  

## <a name="next-steps"></a>Passos seguintes  

* [Configurar um cliente NFS para o Azure NetApp Files](configure-nfs-clients.md)
* [Resolver problemas de volumes de protocolo duplo](troubleshoot-dual-protocol-volumes.md)
