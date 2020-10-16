---
title: Configure um grupo de trabalho independente de domínio
description: Saiba como configurar um grupo de trabalho independente de domínio do Ative Directory Always On availability group numa máquina virtual SQL Server em Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 43b0f64a1d88a71b221fac240392dc71b93eef76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91298840"
---
# <a name="configure-a-workgroup-availability-group"></a>Configure um grupo de trabalho disponibilidade 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo explica as etapas necessárias para criar um cluster independente de domínio do Diretório Ativo com um grupo de disponibilidade Always On; isto também é conhecido como um cluster de grupo de trabalho. Este artigo centra-se nos passos relevantes para a preparação e configuração do grupo de trabalho e do grupo de disponibilidade, e glosss sobre etapas que são abrangidas por outros artigos, como como criar o cluster, ou implementar o grupo de disponibilidade. 


## <a name="prerequisites"></a>Pré-requisitos

Para configurar um grupo de disponibilidade de grupo de trabalho, precisa do seguinte:
- Pelo menos duas máquinas virtuais do Windows Server 2016 (ou superior) que executam o SQL Server 2016 (ou superior), implantadas para o mesmo conjunto de disponibilidade, ou diferentes zonas de disponibilidade, utilizando endereços IP estáticos. 
- Uma rede local com um mínimo de 4 endereços IP gratuitos na sub-rede. 
- Uma conta em cada máquina do grupo de administradores que também tem direitos sysadmin dentro do SQL Server. 
- Portas abertas: TCP 1433, TCP 5022, TCP 59999. 

Para referência, são utilizados neste artigo os seguintes parâmetros, mas podem ser modificados conforme necessário: 

| **Nome** | **Parâmetro** |
| :------ | :---------------------------------- |
| **Nó1**   | AGNode1 (10.0.0.4) |
| **Nó2**   | AGNode2 (10.0.0.5) |
| **Nome do cluster** | AGWGAG (10.0.0.6) |
| **Serviço de Escuta** | AGListener (10.0.0.7) | 
| **Sufixo DNS** | ag.wgcluster.example.com | 
| **Nome do grupo de trabalho** | GRUPO AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-a-dns-suffix"></a>Desaça um sufixo DNS 

Neste passo, configurar o sufixo DNS para ambos os servidores. Por exemplo, `ag.wgcluster.example.com`. Isto permite-lhe utilizar o nome do objeto a que pretende ligar como um endereço totalmente qualificado dentro da sua rede, tal como `AGNode1.ag.wgcluster.example.com` . 

Para configurar o sufixo DNS, siga estes passos:

1. RDP no seu primeiro nó e gestor de servidor aberto. 
1. Selecione **O Servidor Local** e, em seguida, selecione o nome da sua máquina virtual sob o nome de **Computador**. 
1. Selecione **Change...** em **"To rename this computer"**... . 
1. Mude o nome do nome do grupo de trabalho para ser algo significativo, `AGWORKGROUP` como: 

   ![Alterar nome do grupo de trabalho](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. Selecione **Mais...** para abrir a caixa de diálogo **DNS Sfixix e NetBIOS Computer Name.** 
1. Digite o nome do seu sufixo DNS sob **o sufixo de DNS primário deste computador,** tal como `ag.wgcluster.example.com` e, em seguida, selecione **OK**: 

   ![Adicione sufixo DNS](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. Confirme que o **nome do computador completo** está agora a mostrar o sufixo DNS e, em seguida, selecione **OK** para guardar as suas alterações: 

   ![Adicione sufixo DNS](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. Reinicie o servidor quando lhe for solicitado que o faça. 
1. Repita estes passos em quaisquer outros nós a utilizar para o grupo de disponibilidade. 

## <a name="edit-a-host-file"></a>Editar um ficheiro de anfitrião

Uma vez que não existe um diretório ativo, não há forma de autenticar ligações de janelas. Como tal, atribua confiança editando o ficheiro do anfitrião com um editor de texto. 

Para editar o ficheiro do anfitrião, siga estes passos:

1. RDP na sua máquina virtual. 
1. Use **o File Explorer** para ir a `c:\windows\system32\drivers\etc` . 
1. Clique com o botão direito no ficheiro dos **anfitriões** e abra o ficheiro com **o Notepad** (ou qualquer outro editor de texto).
1. No final do ficheiro, adicione uma entrada para cada nó, o grupo de disponibilidade e o ouvinte sob a forma `IP Address, DNS Suffix #comment` de: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Adicione entradas para o endereço IP, cluster e ouvinte ao ficheiro anfitrião](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>Definir permissões

Uma vez que não existe um Ative Directory para gerir permissões, é necessário permitir manualmente uma conta de administrador local não construída para criar o cluster. 

Para tal, executar o seguinte cmdlet PowerShell numa sessão administrativa da PowerShell em cada nó: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Criar o cluster de ativação pós-falha

Neste passo, criará o aglomerado de falhanços. Se não estiver familiarizado com estes passos, pode segui-los a partir do tutorial do [cluster de failover.](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

Diferenças notáveis entre o tutorial e o que deve ser feito para um cluster de grupo de trabalho:
- Desmarque **o armazenamento**e **os espaços de armazenamento diretos** ao executar a validação do cluster. 
- Ao adicionar os nóns ao cluster, adicione o nome totalmente qualificado, tais como:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- DesmarcaR **Adicione todo o armazenamento elegível ao cluster.** 

Uma vez criado o cluster, atribua um endereço IP de cluster estático. Para o fazer, siga estes passos:

1. Num dos nós, abra o **Failover Cluster Manager,** selecione o cluster, clique com o botão direito no ** \<ClusterNam> Nome:** em **Cluster Core Resources** e, em seguida, selecione **Propriedades**. 

   ![Propriedades de lançamento para o nome do cluster](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. Selecione o endereço IP em **endereços IP** e selecione **Editar.** 
1. Selecione **Use Static**, forneça o endereço IP do cluster e, em seguida, selecione **OK**: 

   ![Fornecer um endereço IP estático para o cluster](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. Verifique se as suas definições parecem corretas e, em seguida, selecione **OK** para as guardar:

   ![Verificar propriedades de cluster](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Criar uma testemunha em nuvem 

Neste passo, configuure uma testemunha partilhada em nuvem. Se não estiver familiarizado com os passos, consulte [Implementar uma Testemunha de Nuvem para um Cluster de Falhas.](/windows-server/failover-clustering/deploy-cloud-witness) 

## <a name="enable-the-availability-group-feature"></a>Ativar a funcionalidade de grupo de disponibilidade 

Neste passo, ativar a funcionalidade de grupo de disponibilidade. Se não está familiarizado com os passos, consulte o tutorial do [grupo de disponibilidade.](availability-group-manually-configure-tutorial.md#enable-availability-groups) 

## <a name="create-keys-and-certificates"></a>Criar chaves e certificados

Neste passo, crie certificados que um login SQL utiliza no ponto final encriptado. Crie uma pasta em cada nó para conter as cópias de segurança do certificado, tais como `c:\certs` . 

Para configurar o primeiro nó, siga estes passos: 

1. Abra **o SQL Server Management Studio** e ligue-se ao seu primeiro nó, tal como `AGNode1` . 
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

1. Em seguida, crie o ponto final HADR e use o certificado para autenticação executando esta declaração Transact-SQL (T-SQL):

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

1. Utilize o **File Explorer** para ir ao local do ficheiro onde está o seu certificado, tal como `c:\certs` . 
1. Faça manualmente uma cópia do certificado, `AGNode1Cert.crt` como, a partir do primeiro nó, e transfira-o para o mesmo local no segundo nó. 

Para configurar o segundo nó, siga estes passos: 

1. Ligue-se ao segundo nó com o **SQL Server Management Studio,** como `AGNode2` . 
1. Numa nova janela **de consulta,** execute a seguinte declaração Transact-SQL (T-SQL) após a atualização para uma senha complexa e segura: 

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

1. Em seguida, crie o ponto final HADR e use o certificado para autenticação executando esta declaração Transact-SQL (T-SQL):

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

1. Utilize o **File Explorer** para ir ao local do ficheiro onde está o seu certificado, tal como `c:\certs` . 
1. Faça manualmente uma cópia do certificado, `AGNode2Cert.crt` como, a partir do segundo nó, e transfira-o para o mesmo local no primeiro nó. 

Se houver outros nós no cluster, repita estes passos também, alterando os respetivos nomes de certificado. 

## <a name="create-logins"></a>Criar logins

A autenticação do certificado é usada para sincronizar dados em todos os nós. Para permitir isso, crie um login para o outro nó, crie um utilizador para o login, crie um certificado para o login para utilizar o certificado de back-up e, em seguida, conceda a ligação no ponto final de espelhamento. 

Para tal, primeiro executar a seguinte consulta Transact-SQL (T-SQL) no primeiro nó, tais `AGNode1` como: 

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

Em seguida, executar a seguinte consulta Transact-SQL (T-SQL) no segundo nó, tais `AGNode2` como: 

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

Se houver outros nós no cluster, repita estes passos também, alterando o respetivo certificado e os nomes de utilizador. 

## <a name="configure-an-availability-group"></a>Configure um grupo de disponibilidade

Neste passo, configuure o seu grupo de disponibilidade e adicione as suas bases de dados. Não crie um ouvinte neste momento. Se não está familiarizado com os passos, consulte o tutorial do [grupo de disponibilidade.](availability-group-manually-configure-tutorial.md#create-the-availability-group) Certifique-se de iniciar uma falha e não conseguir verificar se tudo está funcionando como deveria. 

   > [!NOTE]
   > Se houver uma falha durante o processo de sincronização, poderá ter de conceder `NT AUTHORITY\SYSTEM` direitos de sysadmin para criar recursos de cluster no primeiro nó, como por exemplo `AGNode1` temporariamente. 

## <a name="configure-a-load-balancer"></a>Configure um equilibrador de carga

Neste último passo, configuure o equilibrador de carga utilizando o [portal Azure](availability-group-load-balancer-portal-configure.md) ou [o PowerShell](availability-group-listener-powershell-configure.md).


## <a name="next-steps"></a>Passos seguintes

Também pode utilizar [o Az SQL VM CLI](availability-group-az-cli-configure.md) para configurar um grupo de disponibilidade. 


