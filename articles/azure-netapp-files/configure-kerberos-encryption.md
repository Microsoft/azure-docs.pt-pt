---
title: Configure encriptação NFSv4.1 Kerberos para ficheiros Azure NetApp / Microsoft Docs
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
ms.date: 10/19/2020
ms.author: b-juche
ms.openlocfilehash: edb084a3539f4ab25f328d4cc59ee4ef3279bf07
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217053"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Configure encriptação NFSv4.1 Kerberos para ficheiros Azure NetApp

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

1.  Siga as instruções na [Criar uma ligação ative diretoria](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection).  

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

Esta secção ajuda-o a compreender o impacto de desempenho de Kerberos na NFSv4.1.

### <a name="available-security-options"></a>Opções de segurança disponíveis 

As opções de segurança atualmente disponíveis para volumes NFSv4.1 são as seguintes: 

* **sec=sys** usa UIDs e GIDs locais uids usando AUTH_SYS para autenticar operações NFS.
* **sec=krb5** utiliza Kerberos V5 em vez de UIDs e GIDs locais unix para autenticar os utilizadores.
* **sec=krb5i** utiliza Kerberos V5 para autenticação do utilizador e realiza verificação de integridade das operações NFS utilizando despesas de verificação seguras para evitar a adulteração de dados.
* **sec=krb5p** utiliza Kerberos V5 para verificação de autenticação e integridade do utilizador. Encripta o tráfego da NFS para evitar o cheiro de tráfego. Esta opção é a configuração mais segura, mas também envolve a maior sobrecarga de desempenho.

### <a name="performance-vectors-tested"></a>Vetores de desempenho testados

Esta secção descreve o impacto de desempenho do lado único do cliente das várias `sec=*` opções.

* O impacto do desempenho foi testado em dois níveis: baixa conusão (baixa carga) e elevada concordância (limites superiores de I/O e produção).  
* Foram testados três tipos de cargas de trabalho:  
    * Pequena operação leitura/escrita aleatória (usando FIO)
    * Leitura/escrita sequencial de grande operação (utilizando o FIO)
    * Metadados pesados de carga de trabalho gerados por aplicações como git

### <a name="expected-performance-impact"></a>Impacto de desempenho esperado 

Existem duas áreas de foco: carga leve e limite superior. As listas que se seguem descrevem a definição de segurança do impacto de desempenho por definição de segurança e cenário por cenário. Todas as comparações são feitas com o `sec=sys` parâmetro de segurança. O teste foi feito num único volume, usando um único cliente. 

Impacto de desempenho do krb5:

* Baixa concordância (r/w):
    * A latência sequencial aumentou 0,3 ms.
    * A latência aleatória de I/O aumentou 0,2 ms.
    * A latência dos metadados I/O aumentou 0,2 ms.
* Alta concordância (r/w): 
    * A produção sequencial máxima foi desacompanhada por krb5.
    * A E/S aleatória máxima diminuiu 30% para cargas de trabalho de leitura pura, com o impacto global a cair para zero à medida que a carga de trabalho muda para a escrita pura. 
    * A carga máxima de metadados diminuiu 30%.

Impacto de desempenho do krb5i: 

* Baixa concordância (r/w):
    * A latência sequencial aumentou 0,5 ms.
    * A latência aleatória de I/O aumentou 0,2 ms.
    * A latência dos metadados I/O aumentou 0,2 ms.
* Alta concordância (r/w): 
    * A produção sequencial máxima diminuiu 70% em termos globais, independentemente da mistura de carga de trabalho.
    * A E/S aleatória máxima diminuiu 50% para cargas de trabalho de leitura pura, com o impacto global a diminuir para 25% à medida que a carga de trabalho passa para a escrita pura. 
    * A carga máxima de metadados diminuiu 30%.

Impacto de desempenho do krb5p:

* Baixa concordância (r/w):
    * A latência sequencial aumentou 0,8 ms.
    * A latência aleatória de I/O aumentou 0,2 ms.
    * A latência dos metadados I/O aumentou 0,2 ms.
* Alta concordância (r/w): 
    * A produção sequencial máxima diminuiu 85% em termos globais, independentemente da mistura de carga de trabalho. 
    * A E/S aleatória máxima diminuiu 65% para cargas de trabalho de leitura pura, com o impacto global a diminuir para 43% à medida que a carga de trabalho passa para a escrita pura. 
    * A carga máxima de metadados diminuiu 30%.

## <a name="next-steps"></a>Passos seguintes  

* [PERGUNTAS Frequentes Sobre Ficheiros Azure NetApp](azure-netapp-files-faqs.md)
* [Criar um volume NFS para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Criar uma ligação ative directy](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)
* [Configurar um cliente NFS para o Azure NetApp Files](configure-nfs-clients.md) 
