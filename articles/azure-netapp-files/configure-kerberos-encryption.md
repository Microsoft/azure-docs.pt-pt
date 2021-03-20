---
title: Configure encriptação NFSv4.1 Kerberos para ficheiros Azure NetApp | Microsoft Docs
description: Descreve como configurar a encriptação NFSv4.1 Kerberos para ficheiros Azure NetApp e o impacto no desempenho.
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
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: 6ff87d046c60f588e133010895ec3e7ce08cb71f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740567"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Configurar a encriptação NFSv4.1 Kerberos para o Azure NetApp Files

O Azure NetApp Files suporta a encriptação do cliente NFS nos modos Kerberos (krb5, krb5i e krb5p) com encriptação AES-256. Este artigo descreve as configurações necessárias para a utilização de um volume NFSv4.1 com encriptação Kerberos.

## <a name="requirements"></a>Requisitos

Os seguintes requisitos aplicam-se à encriptação do cliente NFSv4.1: 

* Conexão Ative Directory Domain Services (AD DS) para facilitar a bilhética Kerberos 
* Criação de registo DNS A/PTR para o cliente e endereços IP do servidor NFS do Azure NetApp
* Um cliente Linux  
    Este artigo fornece orientação para clientes RHEL e Ubuntu.  Outros clientes trabalharão com passos de configuração semelhantes. 
* Acesso ao servidor NTP  
    Pode utilizar um dos controladores de domínio do Controlador de Domínio do Diretório Ativo (AD DC) comumente utilizados.

## <a name="create-an-nfs-kerberos-volume"></a>Criar um volume NFS Kerberos

1.  Siga os passos na [Criação de um volume NFS para ficheiros Azure NetApp](azure-netapp-files-create-volumes.md) para criar o volume NFSv4.1.   

    Na página Criar um Volume, desabrava a versão **NFS para NFSv4.1**, e desembrasse Kerberos para **Enabled**.

    > [!IMPORTANT] 
    > Não é possível modificar a seleção de ativação Kerberos após a criação do volume.

    ![Criar volume NFSv4.1 Kerberos](../media/azure-netapp-files/create-kerberos-volume.png)  

2. Selecione a Política de **Exportação** para combinar com o nível de acesso e segurança pretendido (Kerberos 5, Kerberos 5i ou Kerberos 5p) para o volume.   

    Para o impacto de desempenho da Kerberos, consulte o [impacto de desempenho da Kerberos na NFSv4.1](#kerberos_performance).  

    Também pode modificar os métodos de segurança Kerberos para o volume clicando na Política de Exportação no painel de navegação dos Ficheiros Azure NetApp.

3.  Clique **em 'Rever + Criar'** para criar o volume NFSv4.1.

## <a name="configure-the-azure-portal"></a>Configure o portal Azure 

1.  Siga as instruções na [Criar uma ligação ative diretoria](create-active-directory-connections.md).  

    Kerberos requer que crie pelo menos uma conta de máquina no Ative Directory. A informação de conta que fornece é utilizada para criar as contas tanto para os volumes *SMB* como para os volumes Kerberos da NFSv4.1. Esta conta é criada automaticamente durante a criação de volume.

2.  No **Reino de Kerberos,** insira o **Nome do Servidor de AD** e o endereço **IP KDC.**

    O AD Server e o KDC IP podem ser o mesmo servidor. Estas informações são utilizadas para criar a conta de máquina SPN utilizada pelos Ficheiros Azure NetApp. Após a criação da conta de máquina, o Azure NetApp Files utilizará registos dns server para localizar servidores KDC adicionais, conforme necessário. 

    ![Reino de Kerberos](../media/azure-netapp-files/kerberos-realm.png)
 
3.  Clique **em Juntar-se** para guardar a configuração.

## <a name="configure-active-directory-connection"></a>Configurar ligação de Diretório Ativo 

Configuração de NFSv4.1 Kerberos cria duas contas de computador no Ative Directory:
* Uma conta de computador para ações SMB
* Uma conta de computador para NFSv4.1-- Pode identificar esta conta através do prefixo `NFS-` . 

Depois de criar o primeiro volume NFSv4.1 Kerberos, dedique o tipo de encriptação para a conta do computador utilizando o seguinte comando PowerShell:

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>Configure o cliente NFS 

Siga as instruções em [Configurar um cliente NFS para a Azure NetApp Files](configure-nfs-clients.md) para configurar o cliente NFS.  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>Monte o volume NFS Kerberos

1. Na página **Volumes,** selecione o volume NFS que pretende montar.

2. Selecione **instruções** de montagem do volume para visualizar as instruções.

    Por exemplo: 

    ![Monte instruções para volumes Kerberos](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. Crie o diretório (ponto de montagem) para o novo volume.  

4. Desafine o tipo de encriptação padrão para AES 256 para a conta do computador:  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * Só precisa de executar este comando uma vez para cada conta de computador.
    * Pode executar este comando a partir de um controlador de domínio ou de um PC com [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) instalado. 
    * A `$NFSCOMPUTERACCOUNT` variável é a conta de computador criada no Ative Directory quando implementa o volume Kerberos. Esta é a conta que está `NFS-` prefixada. 
    * A `$ANFSERVICEACCOUNT` variável é uma conta de utilizador ative não privilegiada com controlos delegados sobre a Unidade Organizacional onde a conta de computador foi criada. 

5. Monte o volume no hospedeiro: 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * A `$ANFEXPORT` variável é o `host:/export` caminho encontrado nas instruções de montagem.
    * A `$ANFMOUNTPOINT` variável é a pasta criada pelo utilizador no anfitrião Linux.

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>Impacto de desempenho da Kerberos no NFSv4.1 

Deve compreender as opções de segurança disponíveis para os volumes NFSv4.1, os vetores de desempenho testados e o impacto de desempenho esperado dos kerberos. Consulte [o impacto de desempenho da Kerberos nos volumes NFSv4.1](performance-impact-kerberos.md) para obter mais detalhes.  

## <a name="next-steps"></a>Passos seguintes  

* [Impacto de desempenho dos Kerberos nos volumes NFSv4.1](performance-impact-kerberos.md)
* [Problemas de resolução NFSv4.1 Problemas de volume Kerberos](troubleshoot-nfsv41-kerberos-volumes.md)
* [PERGUNTAS Frequentes Sobre Ficheiros Azure NetApp](azure-netapp-files-faqs.md)
* [Criar um volume NFS para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Criar uma ligação ative directy](create-active-directory-connections.md)
* [Configurar um cliente NFS para o Azure NetApp Files](configure-nfs-clients.md) 
