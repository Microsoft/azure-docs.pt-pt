---
title: Configure um laboratório para usar gateway de ambiente de trabalho remoto em Laboratórios Azure DevTest
description: Aprenda a configurar um laboratório em Azure DevTest Labs com uma porta remota de ambiente de trabalho para garantir o acesso seguro aos VMs do laboratório sem ter que expor a porta RDP.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: eac195babebf300aa9770d35b7b98eba29c234cf
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460992"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Configure o seu laboratório em Azure DevTest Labs para usar um gateway remoto de ambiente de trabalho
No Azure DevTest Labs, pode configurar uma porta remota de ambiente de trabalho para o seu laboratório para garantir o acesso seguro às máquinas virtuais do laboratório (VMs) sem ter de expor a porta RDP. O laboratório fornece um lugar central para os utilizadores do laboratório verem e ligarem-se a todas as máquinas virtuais a que têm acesso. O botão **Connect** na página **Máquina Virtual** cria um ficheiro RDP específico da máquina que pode abrir para ligar à máquina. Pode ainda personalizar e fixar a ligação RDP ligando o seu laboratório a um gateway remoto de ambiente de trabalho. 

Esta abordagem é mais segura porque o utilizador do laboratório autentica diretamente na máquina de gateway ou pode usar credenciais da empresa numa máquina de gateway unida pelo domínio para se ligar às suas máquinas. O laboratório também suporta o uso da autenticação simbólica para a máquina de gateway que permite aos utilizadores conectarem-se às suas máquinas virtuais de laboratório sem que a porta RDP seja exposta à internet. Este artigo passa por um exemplo sobre como criar um laboratório que usa autenticação simbólica para se conectar a máquinas de laboratório.

## <a name="architecture-of-the-solution"></a>Arquitetura da solução

![Arquitetura da solução](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. A ação de conteúdo do [ficheiro Get RDP](/rest/api/dtl/virtualmachines/getrdpfilecontents) é chamada quando selecionar o botão **Connect.1.** 
1. A ação de conteúdo do `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` ficheiro Get RDP invoca para solicitar um símbolo de autenticação.
    1. `{gateway-hostname}`é o nome de anfitrião do portal gateway especificado na página **Definições** do Laboratório para o seu laboratório no portal Azure. 
    1. `{lab-machine-name}`é o nome da máquina que está a tentar ligar.
    1. `{port-number}`é a porta em que a ligação tem de ser feita. Normalmente este porto é 3389. Se o VM do laboratório estiver a usar a funcionalidade [IP partilhada](devtest-lab-shared-ip.md) nos Laboratórios DevTest, a porta será diferente.
1. O gateway remoto do ambiente `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` de trabalho adia a chamada de uma função Azure para gerar o símbolo de autenticação. O serviço DevTest Labs inclui automaticamente a chave de função no cabeçalho do pedido. A chave da função é ser guardada no cofre do laboratório. O nome para esse segredo será mostrado como segredo de **ficha gateway** na página de **Definições** do Laboratório para o laboratório.
1. Espera-se que a função Azure devolva um símbolo para autenticação simbólica baseada em certificado seletiva contra a máquina gateway.  
1. A ação de conteúdo do ficheiro Get RDP devolve então o ficheiro RDP completo, incluindo as informações de autenticação.
1. Abre o ficheiro RDP utilizando o seu programa de ligação RDP preferido. Lembre-se que nem todos os programas de ligação RDP suportam a autenticação simbólica. O símbolo de autenticação tem uma data de validade, definida pela aplicação de funções. Faça a ligação com o vm do laboratório antes que o token expire.
1. Uma vez que a máquina de porta de entrada remota de ambiente de trabalho autentica o token no ficheiro RDP, a ligação é reencaminhada para a sua máquina de laboratório.

### <a name="solution-requirements"></a>Requisitos da solução
Para trabalhar com a funcionalidade de autenticação token DevTest Labs, existem alguns requisitos de configuração para as máquinas de gateway, serviços de nome de domínio (DNS) e funções.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Requisitos para máquinas remotas de gateway seleção de ambiente de trabalho
- O certificado TLS/SSL deve ser instalado na máquina de porta de entrada para manusear o tráfego HTTPS. O certificado deve corresponder ao nome de domínio totalmente qualificado (FQDN) do equilibrante de carga para a exploração de gateway ou o FQDN da própria máquina se houver apenas uma máquina. Os certificados TLS/SSL não funcionam.  
- Um certificado de assinatura instalado nas máquinas gateway. Crie um certificado de assinatura utilizando o script [Create-SigningCertificate.ps1.](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)
- Instale o módulo de [autenticação pluggable](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) que suporta a autenticação simbólica para o gateway remoto do ambiente de trabalho. Um exemplo de tal `RDGatewayFedAuth.msi` módulo é que vem com imagens do [System Center Virtual Machine Manager (VMM).](/system-center/vmm/install-console?view=sc-vmm-1807) Para mais informações sobre o System Center, consulte a [documentação do System Center](https://docs.microsoft.com/system-center/) e [detalhes de preços.](https://www.microsoft.com/cloud-platform/system-center-pricing)  
- O servidor de gateway pode `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`lidar com pedidos feitos para .

    O nome de gateway-hosté o FQDN do equilibrador de carga da quinta de gateway ou o FQDN da própria máquina se houver apenas uma máquina. É `{lab-machine-name}` o nome da máquina de laboratório que está `{port-number}` a tentar ligar, e a porta é na qual a ligação será feita.  Por defeito, esta porta é 3389.  No entanto, se a máquina virtual estiver a utilizar a funcionalidade [IP partilhada](devtest-lab-shared-ip.md) nos Laboratórios DevTest, a porta será diferente.
- O módulo de pedido de [encaminhamento](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) de aplicações para `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` o Servidor de Informações da Internet (IIS) pode ser utilizado para redirecionar pedidos para a função azure, que trata do pedido para obter um símbolo para autenticação.


## <a name="requirements-for-azure-function"></a>Requisitos para função Azure
A função Azure trata `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` do pedido com formato e devolve o token de autenticação com base no mesmo certificado de assinatura instalado nas máquinas gateway. O `{function-app-uri}` uri usado para aceder à função. A chave de função é automaticamente passada no cabeçalho do pedido. Para uma função [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)de amostra, consulte . 


## <a name="requirements-for-network"></a>Requisitos para a rede

- Os DNS para o FQDN associados ao certificado TLS/SSL instalado nas máquinas de gateway devem direcionar o tráfego para a máquina de gateway ou para o equilibrista de carga da exploração de máquinas gateway.
- Se a máquina de laboratório utilizar iPs privados, deve haver um caminho de rede desde a máquina de gateway até à máquina de laboratório, seja através da partilha da mesma rede virtual ou da utilização de redes virtuais esparsas.

## <a name="configure-the-lab-to-use-token-authentication"></a>Configure o laboratório para usar a autenticação simbólica 
Esta secção mostra como configurar um laboratório para utilizar uma máquina remota de gateway de ambiente de trabalho que suporta a autenticação simbólica. Esta secção não cobre como configurar uma quinta remota de gateways de ambiente de trabalho. Para obter essa informação, consulte a [Amostra para criar uma](#sample-to-create-a-remote-desktop-gateway) secção remota de gateway no final deste artigo. 

Antes de atualizar as definições do laboratório, guarde a chave necessária para executar com sucesso a função para devolver um símbolo de autenticação no cofre chave do laboratório. Pode obter o valor chave da função na página **Gerir** para a função no portal Azure. Para obter mais informações sobre como guardar um segredo num cofre chave, consulte [Adicionar um segredo ao Cofre chave.](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) Guarde o nome do segredo para uso posterior.

Para encontrar a identificação do cofre chave do laboratório, execute o seguinte comando Azure CLI: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Configure o laboratório para utilizar a autenticação simbólica utilizando estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Da lista de laboratórios, selecione o seu **laboratório.**
1. Na página do laboratório, selecione **Configuração e políticas.**
1. No menu esquerdo, na secção **Definições,** selecione **definições de Laboratório**.
1. Na secção **remote desktop,** introduza o nome de domínio totalmente qualificado (FQDN) ou endereço IP da máquina de gateway ou quinta dos serviços de secretária remotos para o campo **gateway hostname.** Este valor deve corresponder ao FQDN do certificado TLS/SSL utilizado nas máquinas gateway.

    ![Opções remotas de ambiente de trabalho em configurações de laboratório](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. Na secção de ambiente de **trabalho Remote,** para o segredo de **token gateway,** insira o nome do segredo criado anteriormente. Este valor não é a chave da função em si, mas o nome do segredo no cofre chave do laboratório que contém a chave da função.

    ![Segredo de ficha de gateway em configurações de laboratório](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Salvar** As mudanças.

    > [!NOTE] 
    > Ao clicar em **Guardar,** concorda com os [termos de licença do Remote Desktop Gateway](https://www.microsoft.com/licensing/product-licensing/products). Para mais informações sobre gateway remoto, consulte [Welcome to Remote Desktop Services](https://aka.ms/rds) e [implemente o seu ambiente de trabalho remoto](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Se configurar o laboratório através da automatização é preferível, consulte [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) para obter um script PowerShell de amostra para definir o nome de **gateway** e as definições secretas de **gateway token.** O [repositório Azure DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab) também fornece um modelo de Gestor de Recursos Azure que cria ou atualiza um laboratório com o nome de **anfitrião** do portal e configurações **secretas de gateway token.**

## <a name="configure-network-security-group"></a>Configure grupo de segurança de rede
Para proteger ainda mais o laboratório, um grupo de segurança de rede (NSG) pode ser adicionado à rede virtual utilizada pelas máquinas virtuais do laboratório. Para obter instruções sobre como configurar um NSG, consulte [Criar, alterar ou eliminar um grupo](../virtual-network/manage-network-security-group.md)de segurança de rede .

Aqui está um exemplo de NSG que só permite o tráfego que passa pela porta de entrada para chegar a máquinas de laboratório. A fonte nesta regra é o endereço IP da máquina de gateway única, ou o endereço IP do equilibrista de carga em frente às máquinas de gateway.

![Grupo de segurança da rede - regras](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Amostra para criar um gateway remoto de ambiente de trabalho

> [!NOTE] 
> Ao utilizar os modelos de amostra, concorda com os [termos de licença do Remote Desktop Gateway](https://www.microsoft.com/licensing/product-licensing/products). Para mais informações sobre gateway remoto, consulte [Welcome to Remote Desktop Services](https://aka.ms/rds) e [implemente o seu ambiente de trabalho remoto](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

O [repositório Azure DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab) fornece algumas amostras para ajudar a configurar os recursos necessários para usar a autenticação simbólica e gateway de ambiente de trabalho remoto com a DevTest Labs. Estas amostras incluem modelos de Gestor de Recursos Azure para máquinas de gateway, configurações de laboratório e aplicação de função.

Siga estes passos para configurar uma solução de amostra para a quinta remota de gateway seletiva.

1. Crie um certificado de assinatura.  Executar [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Guarde a impressão digital, a palavra-passe e a codificação base64 do certificado criado.
2. Obtenha um certificado TLS/SSL. A FQDN associada ao certificado TLS/SSL deve ser para o domínio que controla. Guarde a impressão digital, a palavra-passe e a codificação base64 para este certificado. Para obter a impressão digital utilizando o PowerShell, utilize os seguintes comandos.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Para obter a codificação Base64 utilizando powerShell, utilize o seguinte comando.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Descarregue [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)ficheiros a partir de .

    O modelo requer acesso a alguns outros modelos do Gestor de Recursos e recursos relacionados na mesma base URI. Copie todos [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) os ficheiros de e RDGatewayFedAuth.msi para um recipiente de blob numa conta de armazenamento.  
4. **Desloque o azuredeploy.json** a partir de [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway). O modelo leva os seguintes parâmetros:
    - adminUsername – Obrigatório.  Nome de utilizador administrador para as máquinas gateway.
    - adminPassword – Obrigatório. Palavra-passe para a conta do administrador para as máquinas de gateway.
    - casosCount – Número de máquinas de gateway para criar.  
    - sempreOn – Indica se manter a aplicação azure functions criada em estado quente ou não. Manter a aplicação Funções Azure evitará atrasos quando os utilizadores tentam ligar-se ao seu VM de laboratório, mas isso tem implicações de custos.  
    - tokenLifetime – O tempo que o token criado será válido. O formato é HH:MM:SS.
    - sslCertificate – A codificação Base64 do certificado TLS/SSL para a máquina gateway.
    - sslCertificatePassword – A palavra-passe do certificado TLS/SSL para a máquina gateway.
    - sslCertificateThumbprint - A impressão digital do certificado para identificação no certificado local do certificado TLS/SSL.
    - sinalCertificado – A codificação Base64 para certificado de assinatura para a máquina gateway.
    - signCertificatePassword – A palavra-passe para assinar certificado para a máquina gateway.
    - signCertificateThumbprint - A impressão digital do certificado para identificação no certificado local do certificado de assinatura.
    - _artifactsLocation – Localização URI onde todos os recursos de apoio podem ser encontrados. Este valor deve ser um UIR totalmente qualificado, não um caminho relativo.
    - _artifactsLocationSasToken – O símbolo da Assinatura de Acesso Partilhado (SAS) usado para aceder a recursos de apoio, se a localização for uma conta de armazenamento Azure.

    O modelo pode ser implantado utilizando o Azure CLI utilizando o seguinte comando:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Aqui estão as descrições dos parâmetros:

    - O {ponto final da conta de armazenamento} `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`pode ser obtido executando .  O nome {storage-acct} é o nome da conta de armazenamento que contém ficheiros que fez o upload.  
    - O {nome do recipiente} é o nome do recipiente no nome {storage-acct} que contém ficheiros que carregou.  
    - O {sas-token} pode ser `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`obtido correndo . 
        - O nome {storage-acct} é o nome da conta de armazenamento que contém ficheiros que fez o upload.  
        - O {nome do recipiente} é o nome do recipiente no nome {storage-acct} que contém ficheiros que carregou.  
        - A data de validade {utc} é a data, na UTC, na qual expirará o token SAS e o token SAS já não pode ser utilizado para aceder à conta de armazenamento.

    Grave os valores para gatewayFQDN e gatewayIP a partir da saída de implementação do modelo. Também terá de guardar o valor da chave de função para a função recém-criada, que pode ser encontrada no separador de definições da [aplicação Fun.](../azure-functions/functions-how-to-use-azure-function-app-settings.md)
5. Configure dNS de modo a que fQDN de TLS/SSL cert direcione para o endereço IP de gatewayIP a partir de passo anterior.

    Após a criação da quinta Remote Desktop Gateway e as atualizações adequadas do DNS, está pronta para ser usada por um laboratório em DevTest Labs. O nome de **anfitrião** do portal e as definições secretas de **gateway** devem ser configuradas para utilizar a ou as máquinas de gateway que implementou. 

    > [!NOTE]
    > Se a máquina de laboratório utilizar iPs privados, deve haver um caminho de rede desde a máquina de porta de entrada até à máquina de laboratório, seja através da partilha da mesma rede virtual ou da utilização de uma rede virtual esparsa.

    Uma vez configurado tanto o gateway como o laboratório, o ficheiro de ligação criado quando o utilizador do laboratório clica no **Connect** incluirá automaticamente as informações necessárias para se ligar usando a autenticação simbólica.     

## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte artigo para saber mais sobre os Serviços de Ambiente de Trabalho Remoto: [Documentação dos Serviços de Ambiente](/windows-server/remote/remote-desktop-services/Welcome-to-rds) de Trabalho Remoto


