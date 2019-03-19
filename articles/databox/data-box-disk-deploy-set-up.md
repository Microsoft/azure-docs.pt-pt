---
title: Configurar o Microsoft Azure Data Box Disk | Microsoft Docs
description: Utilize este tutorial para aprender a configurar o Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 4c2f2ebca40ee3e4b5a9a32767783694af52944b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106649"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Tutorial: Descompactar, ligar e desbloquear o disco do Azure Data Box

Este tutorial descreve como descompactar, ligar e desbloquear o seu Disco do Azure Data Box.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Descompactar o Disco do Data Box
> * Ligar a discos e obter a chave de acesso
> * Desbloquear discos no cliente Windows
> * Desbloquear discos no cliente Linux

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Concluiu o [Tutorial: Solicite o disco do Azure Data Box](data-box-disk-deploy-ordered.md).
2. Recebeu os seus discos e o estado da tarefa no portal foi atualizado para **Entregue**.
3. Possui um computador cliente no qual pode instalar a ferramenta de desbloqueio do Data Box Disk. O computador cliente tem de:
    - Executar um [sistema operativo suportado](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Ter outro [software necessário](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) instalado se for um cliente Windows.  

## <a name="unpack-your-disks"></a>Descompactar os seus discos

 Siga os seguintes passos para descompactar os discos.

1. Os Discos do Data Box são enviados por e-mail numa pequena Caixa de envio. Abra a caixa e remova os seus conteúdos. Verifique se a caixa tem 1 a 5 discos de estado sólido (SSDs) e um cabo de ligação USB por disco. Inspecione a caixa em busca de qualquer evidência de adulteração ou outros danos óbvios. 

    ![Pacote de envio do Disco do Data Box](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Se a caixa de envio estiver violada ou gravemente danificada, não abra a caixa. Entre em contacto com o Suporte da Microsoft para ajudar a avaliar se os discos estão a funcionar corretamente e se é preciso enviar uma substituição.
3. Certifique-se de que a caixa tem uma capa clara que contém uma etiqueta de envio (sob a etiqueta atual) para fazer a devolução. Se esta etiqueta estiver em falta ou danificada, pode transferir e imprimir uma nova no portal do Azure. 

    ![Etiqueta de envio do Disco do Data Box](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Guarde a caixa e a espuma do pacote para devolver os discos.

## <a name="connect-to-disks-and-get-the-passkey"></a>Ligar a discos e obter a chave de acesso 

1. Utilize o cabo incluído para ligar o disco ao computador cliente com um SO suportado, conforme indicado nos pré-requisitos. 

    ![Ligação do Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. No portal do Azure, aceda a **Geral > Detalhes do dispositivo**. Utilize o ícone de cópia para copiar a chave de acesso. Esta chave de acesso será utilizada para desbloquear os discos.

    ![Chave de acesso de desbloqueio do Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png) 

Dependendo se está ligado a um cliente Windows ou Linux, os passos para desbloquear os discos são diferentes.

## <a name="unlock-disks-on-windows-client"></a>Desbloquear discos no cliente Windows

Siga os seguintes passos para ligar e desbloquear os discos.
     
1. No portal do Azure, aceda a **Geral > Detalhes do dispositivo**. 
2. Transfira o conjunto de ferramentas do Data Box Disk correspondente ao cliente Windows. Este conjunto de ferramentas contém 3 ferramentas: Ferramenta de caixa desbloquear de disco de dados, a ferramenta de validação de disco de caixa de dados e ferramenta de cópia de divisão de disco de caixa de dados. 

    Neste procedimento, utilizará apenas a ferramenta de Desbloqueio do Data Box Disk. As outras duas ferramentas serão utilizadas posteriormente.

    > [!div class="nextstepaction"]
    > [Transferir o conjunto de ferramentas do Data Box Disk para Windows](https://aka.ms/databoxdisktoolswin)         

3. Extraia o conjunto de ferramentas no mesmo computador que vai utilizar para copiar os dados. 
4. Abra uma janela da Linha de Comandos ou execute o Windows PowerShell como administrador no mesmo computador.
5. (Opcional) Para verificar se o computador que está a utilizar para desbloquear o disco cumpre os requisitos do sistema operativo, execute o comando de verificação do sistema. É apresentada abaixo uma saída de exemplo. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

6. Execute `DataBoxDiskUnlock.exe` e forneça a chave de acesso que obteve em [Ligar a discos e obter a chave de acesso](#Connect-to-disks-and-get-the-passkey). A letra de unidade atribuída ao disco é apresentada. É apresentada abaixo uma saída de exemplo.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkey :
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

7. Repita os passos de desbloqueio para quaisquer futuras reinserções de discos. Utilize o comando `help` se precisar de ajuda com a ferramenta de desbloqueio do Data Box Disk.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKey:<passkey_from_Azure_portal>
    
    Example: DataBoxUnlock /PassKey:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKey:        Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
8. Assim que o disco está desbloqueado, pode ver os conteúdos do disco.    

    ![Conteúdos do Disco do Data Box](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="unlock-disks-on-linux-client"></a>Desbloquear discos no cliente Linux

1. No portal do Azure, aceda a **Geral > Detalhes do dispositivo**. 
2. Transfira o conjunto de ferramentas do Data Box Disk correspondente ao cliente Linux.  

    > [!div class="nextstepaction"]
    > [Transferir o conjunto de ferramentas do Data Box Disk para Linux](https://aka.ms/databoxdisktoolslinux) 

3. No cliente Linux, abra um terminal. Navegue para a pasta onde transferiu o software. Altere as permissões de ficheiro para que possa executar estes ficheiros. Escreva o seguinte comando: 

    `chmod +x DataBoxDiskUnlock_x86_64` 
    
    `chmod +x DataBoxDiskUnlock_Prep.sh` 
 
    É apresentada abaixo uma saída de exemplo. Assim que o comando chmod for executado, pode verificar se as permissões de ficheiro foram alteradas, ao executar o comando `ls`. 
 
    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_x86_64  
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh   
        [user@localhost Downloads]$ ls -l  
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock_x86_64  
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```
4. Execute o script para que instale todos os binários necessários para o software de Desbloqueio do Data Box Disk. Utilize `sudo` para executar o comando como raiz. Assim que os binários forem instalados com êxito, verá uma nota para esse efeito no terminal.

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    O script irá primeiro verificar se o computador cliente está a executar um sistema operativo suportado. É apresentada abaixo uma saída de exemplo. 
 
    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh 
        OS = CentOS Version = 6.9 
        Release = CentOS release 6.9 (Final) 
        Architecture = x64 
    
        The script will install the following packages and dependencies. 
        epel-release 
        dislocker 
        ntfs-3g 
        fuse-dislocker 
        Do you wish to continue? y|n :|
    ```
    
 
5. Escreva `y` para continuar a instalação. Os pacotes que o script instala são: 
   - **epel-release** - repositório que contém os três pacotes seguintes. 
   - **dislocker and fuse-dislocker** - este utilitário ajuda a desencriptar discos encriptados por BitLocker. 
   - **ntfs-3g** - pacote que ajuda a montar volumes NTFS. 
 
     Assim que os pacotes forem instalados com êxito, o terminal irá apresentar uma notificação para esse efeito.     
     ```
     Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6 
     ruby-libs.x86 64 0:1.8.7.374-5.el6 
     Complete! 
     Loaded plugins: fastestmirror, refresh-packagekit, security 
     Setting up Remove Process 
     Resolving Dependencies 
     --> Running transaction check 
     ---> Package epel-release.noarch 0:6-8 will be erased --> Finished Dependency Resolution 
     Dependencies Resolved 
     Package        Architecture        Version        Repository        Size 
     Removing:  epel-release        noarch         6-8        @extras        22 k 
     Transaction Summary                                 
     Remove        1 Package(s) 
     Installed size: 22 k 
     Downloading Packages: 
     Running rpmcheckdebug 
     Running Transaction Test 
     Transaction Test Succeeded 
     Running Transaction 
     Erasing : epel-release-6-8.noarch 
     Verifying : epel-release-6-8.noarch 
     Removed: 
     epel-release.noarch 0:6-8 
     Complete! 
     Dislocker is installed by the script. 
     OpenSSL is already installed.
     ```

6. Execute a ferramenta de Desbloqueio do Data Box Disk. Forneça a chave de acesso do portal do Azure que obteve em [Ligar a discos e obter a chave de acesso](#Connect-to-disks-and-get-the-passkey). Opcionalmente, especifique uma lista de volumes encriptados por BitLocker a desbloquear. A chave de acesso e a lista de volumes devem ser especificadas entre aspas. 

    Escreva o seguinte comando.
 
    `sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’ /Volumes:’<list of volumes>’`         

    A saída de exemplo é apresentada abaixo. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Passkey:’qwerqwerqwer’ /Volumes:’/dev/sdbl’ 
    
    START: Mon Aug 13 14:25:49 2018 
    Volumes: /dev/sdbl 
    Passkey: qwerqwerqwer 
    
    Volumes for data copy : 
    /dev/sdbl: /mnt/DataBoxDisk/mountVoll/ 
    END: Mon Aug 13 14:26:02 2018
    ```
    O ponto de montagem do volume para o qual pode copiar os dados é apresentado.

7. Repita os passos de desbloqueio para quaisquer futuras reinserções de discos. Utilize o comando `help` se precisar de ajuda com a ferramenta de desbloqueio do Data Box Disk. 
    
    `sudo ./DataBoxDiskUnlock_x86_64 /Help` 

    A saída de exemplo é apresentada abaixo. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Help  
    START: Mon Aug 13 14:29:20 2018 
    USAGE: 
    sudo DataBoxDiskUnlock /PassKey:’<passkey from Azure_portal>’ 
    
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ 
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ /Volumes:’/dev/sdbl’ 
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean 
    
    /PassKey: This option takes a passkey as input and unlocks all of your disks. 
    Get the passkey from your Data Box Disk order in Azure portal. 
    /Volumes: This option is used to input a list of BitLocker encrypted volumes. 
    /Help: This option provides help on the tool usage and examples. 
    /Unmount: This option unmounts all the volumes mounted by this tool. 
   
    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```
    
8. Assim que o disco estiver desbloqueado, pode ir para o ponto de montagem e ver os conteúdos do disco. Já está pronto para copiar os dados para as pastas *BlockBlob* ou *PageBlob*. 

    ![Conteúdos do Disco do Data Box](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
> * Descompactar o Disco do Data Box
> * Ligar a discos e obter a chave de acesso
> * Desbloquear discos no cliente Windows
> * Desbloquear discos no cliente Linux


Avance para o próximo tutorial para saber como copiar dados no Disco do Data Box.

> [!div class="nextstepaction"]
> [Copiar dados no Disco do Data Box](./data-box-disk-deploy-copy-data.md)

