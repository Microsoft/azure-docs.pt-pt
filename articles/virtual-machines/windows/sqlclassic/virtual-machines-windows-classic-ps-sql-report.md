---
title: Utilizar o PowerShell para criar uma VM com um servidor de relatórios de modo nativo | Documentos da Microsoft
description: 'Este tópico descreve e orienta a implantação e configuração de um servidor de relatórios de modo nativo do SQL Server Reporting Services na máquina Virtual do Azure. '
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 553af55b-d02e-4e32-904c-682bfa20fa0f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: c8b32e1d52768ea8450a2256d92d8cdb09f9fe8b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57445641"
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Utilizar o PowerShell para Criar uma VM do Azure com um Servidor de Relatórios no Modo Nativo
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Este tópico descreve e orienta a implantação e configuração de um servidor de relatórios de modo nativo do SQL Server Reporting Services na máquina Virtual do Azure. Os passos neste documento utilizam uma combinação de passos manuais para criar a máquina virtual e um script do Windows PowerShell para configurar o Reporting Services na VM. O script de configuração inclui a abrir uma porta de firewall para HTTP ou HTTPs.

> [!NOTE]
> Se não necessitar **HTTPS** no servidor de relatórios, **ignorar a etapa 2**.
> 
> Depois de criar a VM no passo 1, aceda à secção Use um script para configurar o servidor de relatórios e o HTTP. Depois de executar o script, o servidor de relatório está pronto a utilizar.

## <a name="prerequisites-and-assumptions"></a>Pré-requisitos e pressupostos
* **Subscrição do Azure**: Verifique se o número de núcleos disponíveis na sua subscrição do Azure. Se criar o tamanho recomendado da VM de **A3**, precisa **4** núcleos disponíveis. Se utilizar um tamanho VM de **A2**, precisa **2** núcleos disponíveis.
  
  * Para verificar o limite de núcleos da sua subscrição, no portal do Azure, clique em definições no painel esquerdo e, em seguida, clique em utilização no menu superior.
  * Para aumentar a quota de núcleos, contacte [suporte do Azure](https://azure.microsoft.com/support/options/). Para informações de tamanho VM, consulte [tamanhos de Máquina Virtual do Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* **Scripts do Windows PowerShell**: O tópico parte do princípio de que tem um conhecimento funcional básico do Windows PowerShell. Para obter mais informações sobre como utilizar o Windows PowerShell, consulte o seguinte:
  
  * [A iniciar o Windows PowerShell no Windows Server](https://docs.microsoft.com/powershell/scripting/setup/starting-windows-powershell)
  * [Introdução ao Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Passo 1: Aprovisionar uma Máquina Virtual do Azure
1. Navegue para o portal do Azure.
2. Clique em **máquinas virtuais** no painel esquerdo.
   
    ![microsoft azure virtual machines](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. Clique em **Novo**.
   
    ![botão novo](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. Clique em **da galeria**.
   
    ![nova vm a partir da Galeria](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. Clique em **SQL Server 2014 RTM Standard – Windows Server 2012 R2** e, em seguida, clique na seta para continuar.
   
    ![seguinte](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Se precisar dos dados do Reporting Services controlado por recurso de subscrições, escolha **SQL Server 2014 RTM Enterprise, Windows Server 2012 R2**. Para obter mais informações sobre as edições do SQL Server e suporte de funcionalidades, consulte [funcionalidades suportadas pelas edições do SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).
6. Sobre o **configuração da Máquina Virtual** página, edite os seguintes campos:
   
   * Se existir mais do que um **data de lançamento de versão**, selecione a versão mais recente.
   * **Nome da máquina virtual**: O nome do computador também é usado na página de configuração seguinte como o nome de DNS do serviço de nuvem predefinido. O nome DNS deve ser exclusivo em todo o serviço do Azure. Considere configurar a VM com um nome de computador que descreve o que a VM é utilizada para. Por exemplo ssrsnativecloud.
   * **Escalão**: Standard
   * **Tamanho: A3** é o tamanho VM recomendado para cargas de trabalho do SQL Server. Se uma VM for utilizada apenas como um servidor de relatórios, um tamanho VM de A2 é suficiente, a menos que o servidor de relatórios ocorre com uma grande carga de trabalho. Para obter informações sobre preços de VM, consulte [preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/).
   * **Novo nome de utilizador**: o nome fornecido é criado como um administrador na VM.
   * **Nova palavra-passe** e **confirmar**. Esta palavra-passe é utilizada para a nova conta de administrador e é recomendado que utilize uma palavra-passe segura.
   * Clique em **Seguinte**. ![next](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. Na página seguinte, edite os seguintes campos:
   
   * **Serviço em nuvem**: selecione **criar um novo serviço Cloud**.
   * **Nome DNS do serviço de nuvem**: Este é o nome DNS público do serviço Cloud que estão associados com a VM. O nome predefinido é o nome digitado para o nome da VM. Se os passos posteriores do tópico, vai criar um certificado SSL fidedigno e, em seguida, o nome DNS é utilizado o valor da "**emitido para**" do certificado.
   * **Região/grupo de afinidade/rede Virtual**: Selecione a região mais próxima dos seus utilizadores finais.
   * **Conta de armazenamento**: Utilize uma conta de armazenamento gerada automaticamente.
   * **Conjunto de disponibilidade**: Nenhum.
   * **Pontos finais** manter os **ambiente de trabalho remoto** e **PowerShell** pontos de extremidade e, em seguida, adicionar ponto final de um HTTP ou HTTPS, dependendo do seu ambiente.
     
     * **HTTP**: As portas públicas e privadas de predefinidas são **80**. Tenha em atenção que se usar uma porta privada diferente da 80, modifique **$HTTPport = 80** no script de http.
     * **HTTPS**: As portas públicas e privadas de predefinidas são **443**. É recomendado de segurança alterar a porta privada e configurar a firewall e o servidor de relatórios para utilizar a porta privada. Para obter mais informações sobre pontos finais, consulte [como a comunicação com uma Máquina Virtual](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Tenha em atenção que se utilizar uma porta sem ser a 443, altere o parâmetro **$HTTPsport = 443** no script de HTTPS.
   * Clique em seguinte. ![seguinte](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. Na última página do assistente, mantenha a predefinição **instalar o agente da VM** selecionado. Os passos neste tópico não utiliza o agente da VM, mas se planeja manter esta VM, o agente da VM e extensões permitirá que aprimorar ele CM.  Para obter mais informações sobre o agente da VM, consulte [agente da VM e extensões – parte 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Um dos ad de extensões instaladas padrão em execução é a extensão de "BGINFO", que apresenta no ambiente de trabalho VM, informações do sistema, como o IP interno e espaço no disco livre.
9. Clique em concluído. ![ok](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. O **Status** da VM apresenta como **inicial (aprovisionamento)** durante o processo de aprovisionamento e, em seguida, é apresentado como **executar** quando a VM é aprovisionado e está pronto para uso.

## <a name="step-2-create-a-server-certificate"></a>Passo 2: Criar um certificado de servidor
> [!NOTE]
> Se não necessitar de HTTPS no servidor de relatórios, pode **ignore o passo 2** e aceda à secção **utilizar script para configurar o servidor de relatório e HTTP**. Utilize o script HTTP para configurar rapidamente o servidor de relatórios e o servidor de relatórios estará pronto a utilizar.

Para utilizar HTTPS na VM, terá de um certificado SSL confiável. Dependendo do seu cenário, pode usar um dos seguintes dois métodos:

* Um certificado SSL válido emitido por uma autoridade de certificação (AC) e a confiança da Microsoft. Os certificados de raiz da AC têm de ser distribuído via Microsoft Root Certificate Program. Para obter mais informações sobre este programa, consulte [Windows e Windows Phone 8 SSL Root Certificate Program (membro CAs)](https://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) e [introdução para o Microsoft Root Certificate Program](https://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).
* Um certificado autoassinado. Certificados autoassinados não são recomendados para ambientes de produção.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Para utilizar um certificado criado por um certificado de autoridade (autoridade de certificação confiável)
1. **Pedir um certificado de servidor para o Web site de uma autoridade de certificação**. 
   
    Pode utilizar o Assistente de certificado de servidor Web para gerar um ficheiro de pedido de certificado (Certreq.txt) que são enviados para uma autoridade de certificação ou para gerar um pedido para uma autoridade de certificação online. Por exemplo, Microsoft Serviços de certificados no Windows Server 2012. Dependendo do nível de garantia de identificação oferecido pelo seu certificado de servidor, é vários dias para vários meses para a autoridade de certificação aprovar o pedido e enviar-lhe um ficheiro de certificado. 
   
    Para obter mais informações sobre o pedido de certificados de servidor, consulte o seguinte: 
   
   * Use [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
   * Ferramentas de segurança para administrar o Windows Server 2012.
     
     [Ferramentas de segurança para administrar o Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > O **emitido para** campo do certificado SSL confiável deve ser igual a **o nome de DNS do serviço Cloud** que utilizou para a nova VM.

2. **Instalar o certificado de servidor no servidor Web**. Neste caso, o servidor Web é a VM que aloja o servidor de relatórios e o Web site é criado em passos posteriores quando configurar o Reporting Services. Para obter mais informações sobre como instalar o certificado de servidor no servidor Web utilizando o snap-in MMC de certificados, consulte [instale um certificado de servidor](https://technet.microsoft.com/library/cc740068).
   
    Se pretender utilizar o script incluído com esse tópico, para configurar o servidor de relatórios, o valor dos certificados **Thumbprint** é necessária como um parâmetro do script. Veja a secção seguinte para obter detalhes sobre como obter o thumbprint do certificado.
3. Atribua o certificado de servidor para o servidor de relatórios. A atribuição é concluída na próxima seção, quando configurar o servidor de relatórios.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Para utilizar o certificado autoassinado de máquinas virtuais
Um certificado autoassinado foi criado na VM quando a VM foi aprovisionada. O certificado tem o mesmo nome que o nome de DNS de VM. Para evitar erros de certificado, é necessário que o certificado é fidedigno na VM em si e também por todos os utilizadores do site.

1. Para confiar na AC de raiz do certificado na Local VM, adicione o certificado para o **autoridades de certificação de raiz fidedigna**. Segue-se um resumo das etapas necessárias. Para obter passos detalhados sobre como confie na AC, consulte [instale um certificado de servidor](https://technet.microsoft.com/library/cc740068).
   
   1. A partir do portal do Azure, selecione a VM e clique em ligar. Consoante a configuração do browser, poderá ser-lhe pedido para guardar um ficheiro. rdp para ligar à VM.
      
       ![ligar à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilize o nome de VM do utilizador, nome de utilizador e palavra-passe que configurou quando criou a VM. 
      
       Por exemplo, na imagem seguinte, o nome da VM é **ssrsnativecloud** e o nome de utilizador é **testuser**.
      
       ![início de sessão inclui o nome da vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Run mmc.exe. Para obter mais informações, consulte [como: Ver os certificados com o Snap-in do MMC](https://msdn.microsoft.com/library/ms788967.aspx).
   3. No aplicativo de console **arquivo** menu, adicione o **certificados** snap-in, selecione **conta de computador** quando lhe for pedido e, em seguida, clique em **próxima**.
   4. Selecione **computador Local** para gerir e, em seguida, clique em **concluir**.
   5. Clique em **Ok** e, em seguida, expanda o **certificados - pessoais** nós e clique em **certificados**. O certificado com o nome depois do nome DNS da VM e termina com **cloudapp.net**. O nome do certificado com o botão direito e clique em **cópia**.
   6. Expanda a **autoridades de certificação de raiz fidedigna** nó e, em seguida, botão direito do mouse **certificados** e, em seguida, clique em **colar**.
   7. Para validar, faça duplo clique no nome do certificado sob **autoridades de certificação de raiz fidedigna** e verifique se não existirem erros e ver o seu certificado. Se pretender utilizar o script HTTPS, incluído com esse tópico, para configurar o servidor de relatório, o valor dos certificados **Thumbprint** é necessária como um parâmetro do script. **Para obter o valor do thumbprint**, efetue o seguinte. Também há um exemplo de PowerShell para obter o thumbprint na seção [utilizar script para configurar o servidor de relatório e o HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
      
      1. Faça duplo clique o nome do certificado, por exemplo ssrsnativecloud.cloudapp.net.
      2. Clique no separador **Detalhes** .
      3. Clique em **Thumbprint**. O valor do thumbprint é apresentado no campo de detalhes, por exemplo a6 08 3C df f9 0b f7 e3 7C 25 ed a4 ed 7e ac 91 9C 2c fb 2f.
      4. Copie o thumbprint e guardar o valor para utilizar mais tarde ou edite o script agora.
      5. (*) Antes de executar o script, remova os espaços entre os pares de valores. Por exemplo o thumbprint indicado anteriormente agora seria a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
      6. Atribua o certificado de servidor para o servidor de relatórios. A atribuição é concluída na próxima seção, quando configurar o servidor de relatórios.

Se estiver a utilizar um certificado SSL autoassinado, o nome do certificado já corresponde ao nome do anfitrião da VM. Por conseguinte, o DNS da máquina já está registado globalmente e podem ser acedido a partir de qualquer cliente.

## <a name="step-3-configure-the-report-server"></a>Passo 3: Configurar o servidor de relatórios
Esta secção explica como configurar a VM como um servidor de relatórios de modo nativo do Reporting Services. Pode utilizar um dos seguintes métodos para configurar o servidor de relatório:

* Utilize o script para configurar o servidor de relatórios
* Utilize o Gestor de configuração para configurar o servidor de relatórios.

Para obter passos mais detalhados, consulte a secção [ligar à máquina Virtual e iniciar o Reporting Services Configuration Manager](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Nota de autenticação:** Autenticação do Windows é o método de autenticação recomendado e é a autenticação do Reporting Services predefinido. Apenas os utilizadores que estão configurados na VM podem acessar o Reporting Services e atribuídos a funções do Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Use um script para configurar o servidor de relatórios e HTTP
Para utilizar o script do Windows PowerShell para configurar o servidor de relatórios, conclua os passos seguintes. A configuração inclui HTTP, HTTPS não:

1. A partir do portal do Azure, selecione a VM e clique em ligar. Consoante a configuração do browser, poderá ser-lhe pedido para guardar um ficheiro. rdp para ligar à VM.
   
    ![ligar à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilize o nome de VM do utilizador, nome de utilizador e palavra-passe que configurou quando criou a VM. 
   
    Por exemplo, na imagem seguinte, o nome da VM é **ssrsnativecloud** e o nome de utilizador é **testuser**.
   
    ![início de sessão inclui o nome da vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Na VM, abra **ISE do Windows PowerShell** com privilégios administrativos. O ISE do PowerShell está instalado por predefinição no servidor do Windows 2012. Recomenda-se que utilizar o ISE em vez de uma janela padrão do Windows PowerShell, para que possa colar o script no ISE, modificar o script e, em seguida, execute o script.
3. No ISE do Windows PowerShell, clique nas **View** menu e clique em **Mostrar painel de Script**.
4. Copie o seguinte script e cole o script para o painel de scripts do ISE do Windows PowerShell.
   
        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
   
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
   
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Report Server Configuration Steps
   
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
   
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
   
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
5. Se criou a VM com uma porta HTTP que não a 80, modifique o parâmetro $HTTPport = 80.
6. O script está atualmente configurado para o Reporting Services. Se quiser executar o script para o Reporting Services, modifique a parte da versão do caminho para o espaço de nomes para "v11", na declaração do Get-WmiObject.
7. Execute o script.

**Validação**: Para verificar se a funcionalidade de servidor de relatório básico está a funcionar, consulte a [verificar a configuração](#verify-the-configuration) seção mais adiante neste tópico.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Use um script para configurar o servidor de relatórios e HTTPS
Para utilizar o Windows PowerShell para configurar o servidor de relatórios, conclua os passos seguintes. A configuração inclui HTTPS, não HTTP.

1. A partir do portal do Azure, selecione a VM e clique em ligar. Consoante a configuração do browser, poderá ser-lhe pedido para guardar um ficheiro. rdp para ligar à VM.
   
    ![ligar à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Utilize o nome de VM do utilizador, nome de utilizador e palavra-passe que configurou quando criou a VM. 
   
    Por exemplo, na imagem seguinte, o nome da VM é **ssrsnativecloud** e o nome de utilizador é **testuser**.
   
    ![início de sessão inclui o nome da vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Na VM, abra **ISE do Windows PowerShell** com privilégios administrativos. O ISE do PowerShell está instalado por predefinição no servidor do Windows 2012. Recomenda-se que utilizar o ISE em vez de uma janela padrão do Windows PowerShell, para que possa colar o script no ISE, modificar o script e, em seguida, execute o script.
3. Para ativar a execução de scripts, execute o seguinte comando Windows PowerShell:
   
        Set-ExecutionPolicy RemoteSigned
   
    Em seguida, pode executar o seguinte para verificar a política:
   
        Get-ExecutionPolicy
4. Na **ISE do Windows PowerShell**, clique nas **vista** menu e clique em **Mostrar painel de Script**.
5. Copie o seguinte script e cole-o para o painel de scripts do ISE do Windows PowerShell.
   
        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
   
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
   
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Reporting Services Report Server Configuration Steps
   
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
   
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
   
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## 3. Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
   
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
   
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
6. Modificar a **$certificatehash** parâmetro no script:
   
   * Este é um **necessário** parâmetro. Se não tiver guardado o valor do certificado dos passos anteriores, utilize um dos seguintes métodos para copiar o valor de hash do certificado do thumbprint de certificados.:
     
       Na VM, abra o ISE do Windows PowerShell e execute o seguinte comando:
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       O resultado será semelhante ao seguinte. Se o script retorna uma linha em branco, a VM não tem um certificado configurado, por exemplo, consulte a secção [para utilizar o certificado autoassinado de máquinas virtuais](#to-use-the-virtual-machines-self-signed-certificate).
     
     OU
   * Na VM, execute mmc.exe e, em seguida, adicione a **certificados** snap-in.
   * Sob o **autoridades de certificação de raiz confiáveis** nó faça duplo clique em seu nome de certificado. Se estiver a utilizar o certificado autoassinado da VM, o certificado com o nome depois do nome DNS da VM e termina com **cloudapp.net**.
   * Clique no separador **Detalhes** .
   * Clique em **Thumbprint**. O valor do thumbprint é apresentado no campo de detalhes, por exemplo af 11 60 b6 4b d de 8 de 28 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
   * **Antes de executar o script**, remova os espaços entre os pares de valores. Por exemplo af1160b64b288d890a8212ff6ba9c3664f319048
7. Modificar a **$httpsport** parâmetro: 
   
   * Se utilizou a porta 443 para o ponto final HTTPS, não é necessário atualizar este parâmetro no script. Caso contrário, utilize o valor da porta que selecionou quando configurou o ponto final privado de HTTPS na VM.
8. Modificar a **$DNSName** parâmetro: 
   
   * O script está configurado para um certificado de caráter universal $DNSName = "+". Se fizer isso não quer configurar para um enlace de certificado de caráter universal, comente $DNSName ="+"e ative a seguinte linha, a referência completa $DNSNAme, # # $DNSName="$server.cloudapp.net".
     
       Altere o valor de $DNSName se não pretender utilizar o nome DNS da máquina virtual para o Reporting Services. Se utilizar o parâmetro, o certificado também tem de utilizar este nome e registar o nome globalmente num servidor DNS.
9. O script está atualmente configurado para o Reporting Services. Se quiser executar o script para o Reporting Services, modifique a parte da versão do caminho para o espaço de nomes para "v11", na declaração do Get-WmiObject.
10. Execute o script.

**Validação**: Para verificar se a funcionalidade de servidor de relatório básico está a funcionar, consulte a verificar a seção de configuração mais adiante neste tópico. Para verificar o certificado de ligação abra uma linha de comandos com privilégios administrativos e, em seguida, execute o seguinte comando:

    netsh http show sslcert

O resultado incluirá o seguinte:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Utilize o Gestor de configuração para configurar o servidor de relatórios
Se não pretender executar o script do PowerShell para configurar o servidor de relatório, siga os passos nesta secção para utilizar o Gestor de configuração de modo nativo do Reporting Services para configurar o servidor de relatórios.

1. A partir do portal do Azure, selecione a VM e clique em ligar. Utilize o nome de utilizador e palavra-passe que configurou quando criou a VM.
   
    ![ligar à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Execute o Windows update e instale atualizações para a VM. Se for necessário um reinício da VM, reinicie a VM e voltar a ligar à VM do portal do Azure.
3. A partir do menu Iniciar na VM, escreva **Reporting Services** e abra **Reporting Services Configuration Manager**.
4. Deixe os valores predefinidos para **nome do servidor** e **instância do servidor de relatórios**. Clique em **Ligar**.
5. No painel esquerdo, clique em **URL do serviço Web**.
6. Por predefinição, o RS está configurado para a porta HTTP 80 com IP "Atribuído todos os". Para adicionar a HTTPS:
   
   1. Na **certificado SSL**: selecione o certificado que pretende utilizar, por exemplo [nome da VM]. cloudapp.net. Se não forem apresentados certificados, consulte a secção **passo 2: Criar um certificado de servidor** para obter informações sobre como instalar e confiar no certificado na VM.
   2. Sob **porta SSL**: escolha 443. Se tiver configurado o ponto final privado de HTTPS na VM com uma porta privada diferente, use esse valor aqui.
   3. Clique em **aplicar** e aguarde pela conclusão da operação.
7. No painel esquerdo, clique em **base de dados**.
   
   1. Clique em **alterar base de Dad**e.
   2. Clique em **criar uma nova base de dados de servidor de relatório** e, em seguida, clique em **próxima**.
   3. Deixe a predefinição **nome do servidor**: como a VM dê um nome e deixe a predefinição **tipo de autenticação** como **utilizador atual** – **segurança integrada**. Clique em **Seguinte**.
   4. Deixe a predefinição **nome da base de dados** como **ReportServer** e clique em **seguinte**.
   5. Deixe a predefinição **tipo de autenticação** como **credenciais de serviço** e clique em **seguinte**.
   6. Clique em **próxima** sobre o **resumo** página.
   7. Quando a configuração estiver concluída, clique em **concluir**.
8. No painel esquerdo, clique em **URL do Gestor de relatórios**. Deixe a predefinição **diretório Virtual** como **relatórios** e clique em **aplicar**.
9. Clique em **saída** para fechar o Reporting Services Configuration Manager.

## <a name="step-4-open-windows-firewall-port"></a>Passo 4: Porta de Firewall do Windows aberto
> [!NOTE]
> Se utilizou um dos scripts para configurar o servidor de relatório, pode ignorar esta secção. O script incluído um passo para abrir a porta de firewall. A predefinição foi a porta 80 para HTTP e a porta 443 para HTTPS.
> 
> 

Para ligar remotamente ao Gestor de relatórios ou o servidor de relatórios na máquina virtual, um ponto final TCP é necessária na VM. É necessário abrir a mesma porta na firewall da VM. O ponto final foi criado quando a VM foi aprovisionada.

Esta seção fornece informações básicas sobre como abrir a porta de firewall. Para obter mais informações, consulte [configurar uma Firewall para acesso ao servidor de relatórios](https://technet.microsoft.com/library/bb934283.aspx)

> [!NOTE]
> Se utilizou o script para configurar o servidor de relatório, pode ignorar esta secção. O script incluído um passo para abrir a porta de firewall.
> 
> 

Se tiver configurado uma porta privada para HTTPS sem ser a 443, modifique o seguinte script de forma adequada. Para abrir a porta **443** na Firewall do Windows, faça o seguinte:

1. Abra uma janela do Windows PowerShell com privilégios administrativos.
2. Se utilizou uma porta diferente da 443 quando configurou o ponto final HTTPS na VM, atualize a porta no comando seguinte e, em seguida, execute o comando:
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. Quando o comando for concluído, **Ok** é apresentado no prompt de comando.

Para verificar que a porta é aberta, abra uma janela do Windows PowerShell e execute o seguinte comando:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Verificar a configuração
Para verificar se a funcionalidade de servidor de relatório básico está a funcionar agora, abra o browser com privilégios administrativos e, em seguida, navegue para o seguintes relatórios ad relatório Gestor de servidor URLS:

* Na VM, navegue para o URL do servidor de relatório:
  
        http://localhost/reportserver
* Na VM, navegue para o URL do relatório Manager:
  
        http://localhost/Reports
* Do computador local, navegue para o **remoto** Gestor de relatórios na VM. Atualize o nome DNS no exemplo a seguir, conforme apropriado. Quando lhe for pedido para uma palavra-passe, utilize as credenciais de administrador que criou quando a VM foi aprovisionada. O nome de utilizador está no [domínio]\[nome de utilizador] formato, onde o domínio é o nome de computador VM, por exemplo ssrsnativecloud\testuser. Se não estiver a utilizar HTTP**S**, remova o **s** no URL. Veja a secção seguinte para obter informações sobre como criar utilizadores adicionais na VM.
  
        https://ssrsnativecloud.cloudapp.net/Reports
* Do computador local, navegue para o URL do servidor de relatórios remoto. Atualize o nome DNS no exemplo a seguir, conforme apropriado. Se não estiver a utilizar o HTTPS, remova o s no URL.
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Criar utilizadores e atribuir funções
Depois de configurar e verificar o servidor de relatórios, uma tarefa administrativa comum é criar um ou mais utilizadores e atribuir utilizadores a funções do Reporting Services. Para obter mais informações, consulte o seguinte:

* [Criar uma conta de utilizador local](https://technet.microsoft.com/library/cc770642.aspx)
* [Acesso de utilizador de concessão para um servidor de relatórios (Gestor de relatórios)](https://msdn.microsoft.com/library/ms156034.aspx))
* [Criar e gerir atribuições de funções](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Para criar e publicar relatórios para a Máquina Virtual do Azure
A tabela seguinte resume algumas das opções disponíveis para publicar relatórios existentes a partir de um computador no local para o servidor de relatórios alojados na máquina de Virtual do Microsoft Azure:

* **Script de RS.exe**: Utilize o script de RS.exe para copiar itens de relatório de e para o servidor de relatórios existentes para sua máquina de Virtual do Microsoft Azure. Para obter mais informações, consulte a seção "Modo nativo para o modo nativo – Microsoft Azure Virtual Machine" no [no Script para migrar conteúdo entre servidores de relatórios do Reporting Services do exemplo rs.exe](https://msdn.microsoft.com/library/dn531017.aspx).
* **Report Builder**: A máquina virtual inclui o clique-uma vez a versão do Microsoft SQL Server Report Builder. Para iniciar o tempo do Report builder a primeira na máquina virtual:
  
  1. Comece o seu browser com privilégios administrativos.
  2. Navegue para o Gestor de relatórios na máquina virtual e clique em **Report Builder** na faixa de opções.
     
     Para obter mais informações, consulte [instalar, desinstalar e que suporta o Report Builder](https://technet.microsoft.com/library/dd207038.aspx).
* **Ferramentas de dados do SQL Server: VM**:  Se criou a VM com o SQL Server 2012, o SQL Server Data Tools está instalado na máquina virtual e podem ser utilizados para criar **projetos de servidor de relatório** e relatórios na máquina virtual. SQL Server Data Tools pode publicar os relatórios para o servidor de relatórios na máquina virtual.
  
    Se criou a VM com o SQL server 2014, pode instalar o SQL Server Data Tools - BI para o visual Studio. Para obter mais informações, consulte o seguinte:
  
  * [Microsoft SQL Server Data Tools – Business Intelligence para o Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)
  * [Microsoft SQL Server Data Tools – Business Intelligence para o Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)
  * [Ferramentas de dados do SQL Server e SQL Server Business Intelligence (SSDT-BI)](https://docs.microsoft.com/sql/ssdt/previous-releases-of-sql-server-data-tools-ssdt-and-ssdt-bi)
* **Ferramentas de dados do SQL Server: Remoto**:  No seu computador local, crie um projeto do Reporting Services no SQL Server Data Tools que contém os relatórios do Reporting Services. Configure o projeto para ligar ao URL de serviço web.
  
    ![Propriedades do projeto SSDT para o projeto do SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **Utilizar script**: Utilize script para copiar o conteúdo do servidor de relatório. Para obter mais informações, consulte [no Script para migrar conteúdo entre servidores de relatórios do Reporting Services do exemplo rs.exe](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Minimizar os custos se não estiver a utilizar a VM
> [!NOTE]
> Para minimizar os custos para as máquinas de virtuais do Azure quando não está em utilização, encerre a VM a partir do portal do Azure. Se utilizar as opções de energia do Windows dentro de uma VM para encerrar a VM, ainda é cobradas a mesma quantia para a VM. Para reduzir os custos, terá de encerrar a VM no portal do Azure. Se já não precisar da VM, não se esqueça de eliminar a VM e os ficheiros. vhd associados para evitar encargos de armazenamento. Para obter mais informações, consulte a seção de perguntas Freqüentes [detalhes de preços das máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>Mais Informações
### <a name="resources"></a>Recursos
* Para obter conteúdo semelhante relacionados com uma implementação de servidor único do SQL Server Business Intelligence e o SharePoint 2013, consulte [utilize o Windows PowerShell para criar uma VM de Azure com o SQL Server BI e o SharePoint 2013](https://blogs.technet.microsoft.com/ptsblog/2013/10/24/use-powershell-to-create-a-windows-azure-vm-with-sql-server-bi-and-sharepoint-2013/).
* Para obter informações gerais relacionadas com implementações do SQL Server Business Intelligence em máquinas de virtuais do Azure, consulte [SQL Server Business Intelligence em máquinas de virtuais do Azure](virtual-machines-windows-classic-ps-sql-bi.md).
* Para obter mais informações sobre o custo de encargos de computação do Azure, consulte o separador de máquinas virtuais do [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Conteúdo da Comunidade
* Para instruções passo a passo sobre como criar um servidor de relatórios de modo nativo do Reporting Services sem utilizar o script, consulte [alojamento do SQL Reporting Service na máquina de Virtual do Azure](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Links para outros recursos para o SQL Server em VMs do Azure
[SQL Server em Descrição geral de máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

