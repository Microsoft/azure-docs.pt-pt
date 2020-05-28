---
title: Configure um grupo de disponibilidade independente de domínio
description: Saiba como configurar um grupo de trabalho independente de domínio ativo Always On grupo de disponibilidade numa máquina virtual SQL Server em Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 36c4a141acf38d83ff925bafaa75c294847a7d74
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84049331"
---
# <a name="configure-a-workgroup-availability-group"></a>Configure um grupo de disponibilidade de grupo de trabalho 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo explica os passos necessários para criar um cluster independente de domínio ative Diretório com um grupo de disponibilidade Always On; este também é conhecido como um cluster de grupo de trabalho. Este artigo centra-se nos passos relevantes para a preparação e configuração do grupo de trabalho e do grupo de disponibilidade, e glosss sobre passos que estão cobertos em outros artigos, como como criar o cluster, ou implementar o grupo de disponibilidade. 


## <a name="prerequisites"></a>Pré-requisitos

Para configurar um grupo de disponibilidade de grupo de trabalho, precisa do seguinte:
- Pelo menos duas máquinas virtuais do Windows Server 2016 (ou superiores) que executam o SQL Server 2016 (ou superior), implementadas para o mesmo conjunto de disponibilidade, ou diferentes zonas de disponibilidade, utilizando endereços IP estáticos. 
- Uma rede local com um mínimo de 4 endereços IP gratuitos na subnet. 
- Uma conta em cada máquina do grupo de administradores que também tem direitos de sisadmina dentro do SQL Server. 
- Portas abertas: TCP 1433, TCP 5022, TCP 59999. 

Para referência, os seguintes parâmetros são utilizados neste artigo, mas podem ser modificados conforme necessário: 

| **Nome** | **Parâmetro** |
| :------ | :---------------------------------- |
| **Nó1**   | AGNode1 (10.0.0.4) |
| **Nó2**   | AGNode2 (10.0.0.5) |
| **Nome do cluster** | AGWGAG (10.0.0.6) |
| **Serviço de Escuta** | AGListener (10.0.0.7) | 
| **Sufixo DNS** | ag.wgcluster.example.com | 
| **Nome do grupo de trabalho** | Grupo AGWork | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>Definir sufixo DNS 

Neste passo, configure o sufixo DNS para ambos os servidores. Por exemplo, `ag.wgcluster.example.com`. Isto permite-lhe utilizar o nome do objeto a que pretende ligar como um endereço totalmente qualificado dentro da sua rede, como `AGNode1.ag.wgcluster.example.com` . 

Para configurar o sufixo DNS, siga estes passos:

1. RDP no seu primeiro nó e abre o Servidor Manager. 
1. Selecione **Local Server** e, em seguida, selecione o nome da sua máquina virtual sob **o nome**do computador . 
1. Selecione **Alteração...** em baixo **Para mudar o nome deste computador...**. 
1. Mude o nome do nome do grupo de trabalho para ser algo significativo, `AGWORKGROUP` como: 

   ![Alterar nome do grupo de trabalho](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. Selecione **Mais...** para abrir a caixa de diálogo **DNS Sufixo e NetBIOS Computer Name.** 
1. Digite o nome do seu sufixo DNS sob o **sufixo DNS primário deste computador,** tal como `ag.wgcluster.example.com` e, em seguida, selecione **OK:** 

   ![Adicione sufixo DNS](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. Confirme que o **nome completo** do computador está agora a mostrar o sufixo DNS e, em seguida, selecione **OK** para guardar as suas alterações: 

   ![Adicione sufixo DNS](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. Reinicie o servidor quando lhe for solicitado que o faça. 
1. Repita estes passos em quaisquer outros nódosos para serem utilizados para o grupo de disponibilidade. 

## <a name="edit-host-file"></a>Editar ficheiro anfitrião

Como não há diretório ativo, não há forma de autenticar as ligações das janelas. Como tal, atribua confiança editando o ficheiro anfitrião com um editor de texto. 

Para editar o ficheiro anfitrião, siga estes passos:

1. RDP na sua máquina virtual. 
1. Use o Explorador de **Ficheiros** para ir a `c:\windows\system32\drivers\etc` . 
1. Clique no ficheiro dos **anfitriões** e abra o ficheiro com **o Bloco** de Notas (ou qualquer outro editor de texto).
1. No final do ficheiro, adicione uma entrada para cada nó, o grupo de disponibilidade e o ouvinte sob a forma `IP Address, DNS Suffix #comment` de: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Adicione entradas para o endereço IP, cluster e ouvinte do ficheiro anfitrião](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>Definir permissões

Uma vez que não existe um Diretório Ativo para gerir permissões, é necessário permitir manualmente uma conta de administrador local não construída para criar o cluster. 

Para isso, execute o seguinte cmdlet PowerShell numa sessão administrativa powerShell em cada nó: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Criar o cluster de ativação pós-falha

Neste passo, criará o cluster failover. Se não está familiarizado com estes passos, pode segui-los a partir do tutorial do [cluster failover](failover-cluster-instance-storage-spaces-direct-manually-configure.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct).

Diferenças notáveis entre o tutorial e o que deve ser feito para um agrupamento de trabalho:
- Desmarque **o armazenamento**e **os espaços de armazenamento diretos** ao executar a validação do cluster. 
- Ao adicionar os nós ao cluster, adicione o nome totalmente qualificado, tais como:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- **Desfaça-se Adicione todo o armazenamento elegível ao cluster**. 

Uma vez criado o cluster, atribua um endereço IP de cluster estático. Para o fazer, siga estes passos:

1. Num dos nós, abra o **Failover Cluster Manager,** selecione o cluster, clique no ** \<ClusterNam> nome:** em Recursos **Core cluster** e, em seguida, selecione **Propriedades**. 

   ![Propriedades de lançamento para o nome do cluster](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. Selecione o endereço IP em **endereços IP** e selecione **Editar**. 
1. Selecione **Utilizar Estática,** forneça o endereço IP do cluster e, em seguida, selecione **OK:** 

   ![Forneça um endereço IP estático para o cluster](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. Verifique se as suas definições parecem corretas e, em seguida, selecione **OK** para as salvar:

   ![Verificar propriedades do cluster](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Criar uma testemunha de nuvem 

Neste passo, configure uma testemunha de partilha de nuvens. Se não está familiarizado com os passos, consulte o tutorial do [cluster failover](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-a-cloud-witness). 

## <a name="enable-availability-group-feature"></a>Ativar a funcionalidade do grupo de disponibilidade 

Neste passo, ative a funcionalidade do grupo de disponibilidade. Se não está familiarizado com os passos, consulte o tutorial do grupo de [disponibilidade.](availability-group-manually-configure-tutorial.md#enable-availability-groups) 

## <a name="create-keys-and-certificate"></a>Criar chaves e certificado

Neste passo, crie certificados que um login SQL utiliza no ponto final encriptado. Crie uma pasta em cada nó para segurar as cópias de segurança do certificado, tais como `c:\certs` . 

Para configurar o primeiro nó, siga estes passos: 

1. Abra o Estúdio de Gestão de **Servidores SQL** e ligue-se ao seu primeiro nó, como `AGNode1` . 
1. Abra uma nova janela **de consulta** e execute a seguinte declaração Transact-SQL (T-SQL) após a atualização para uma senha complexa e segura:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Em seguida, crie o ponto final HADR e utilize o certificado para autenticação executando esta declaração Transact-SQL (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Utilize o **File Explorer** para ir ao local do ficheiro onde está o seu certificado, como `c:\certs` . 
1. Faça manualmente uma cópia do certificado, como, por `AGNode1Cert.crt` exemplo, a partir do primeiro nó, e transfira-o para o mesmo local no segundo nó. 

Para configurar o segundo nó, siga estes passos: 

1. Ligue-se ao segundo nó com o Estúdio de Gestão de **Servidores SQL,** como `AGNode2` . 
1. Numa janela **new consulta,** execute a seguinte declaração Transact-SQL (T-SQL) após a atualização para uma senha complexa e segura: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Em seguida, crie o ponto final HADR e utilize o certificado para autenticação executando esta declaração Transact-SQL (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Utilize o **File Explorer** para ir ao local do ficheiro onde está o seu certificado, como `c:\certs` . 
1. Faça manualmente uma cópia do certificado, como, por `AGNode2Cert.crt` exemplo, a partir do segundo nó, e transfira-o para o mesmo local no primeiro nó. 

Se houver outros nós no cluster, repita estes passos também, modificando os respetivos nomes de certificados. 

## <a name="create-logins"></a>Criar logins

A autenticação do certificado é utilizada para sincronizar dados em todos os nódosos. Para permitir isto, crie um login para o outro nó, crie um utilizador para o login, crie um certificado para o login utilizar o certificado de back-up e, em seguida, conceder a ligação no ponto final espelhado. 

Para tal, primeiro faça a seguinte consulta Transact-SQL (T-SQL) no primeiro nó, tais `AGNode1` como: 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Em seguida, faça a seguinte consulta Transact-SQL (T-SQL) no segundo nó, tais `AGNode2` como: 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Se houver outros nós no cluster, repita esses passos também, modificando o respetivo certificado e nomes de utilizador. 

## <a name="configure-availability-group"></a>Configure grupo de disponibilidade

Neste passo, configure o seu grupo de disponibilidade e adicione as suas bases de dados. Não crie um ouvinte neste momento. Se não está familiarizado com os passos, consulte o tutorial do grupo de [disponibilidade.](availability-group-manually-configure-tutorial.md#create-the-availability-group) Certifique-se de iniciar uma falha e não verificar se está tudo a funcionar como deve ser. 

   > [!NOTE]
   > Se houver uma falha durante o processo de sincronização, poderá ter de conceder direitos de `NT AUTHORITY\SYSTEM` sysadmin a criar recursos de cluster no primeiro nó, como `AGNode1` temporariamente. 

## <a name="configure-load-balancer"></a>Configure o equilibrador de carga

Neste passo final, configure o equilibrador de carga utilizando o [portal Azure](availability-group-load-balancer-portal-configure.md) ou [o PowerShell](availability-group-listener-powershell-configure.md)


## <a name="next-steps"></a>Passos Seguintes

Também pode utilizar [o Az SQL VM CLI](availability-group-az-cli-configure.md) para configurar um grupo de disponibilidade. 


