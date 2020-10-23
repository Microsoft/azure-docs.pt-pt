---
title: Configure um laboratório para usar o Gateway de Desktop Remoto em Azure DevTest Labs
description: Aprenda a configurar um laboratório em Azure DevTest Labs com uma porta de entrada remota para garantir acesso seguro aos VMs de laboratório sem ter de expor a porta RDP.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b48a0709deb21ca0f8a27d1cf953c7d8d4ba2cc8
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144700"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Configure o seu laboratório em Azure DevTest Labs para usar um gateway remoto
Em Azure DevTest Labs, pode configurar uma porta de entrada remota para o seu laboratório para garantir um acesso seguro às máquinas virtuais de laboratório (VMs) sem ter de expor a porta RDP. O laboratório fornece um lugar central para os utilizadores do laboratório verem e conectarem-se a todas as máquinas virtuais a que têm acesso. O botão **Ligar** na página **Máquina Virtual** cria um ficheiro RDP específico para máquinas que pode abrir para ligar à máquina. Pode personalizar e proteger ainda mais a ligação RDP ligando o seu laboratório a um gateway remoto de secretária. 

Esta abordagem é mais segura porque o utilizador do laboratório autentica diretamente para a máquina de gateway ou pode usar credenciais da empresa numa máquina de gateway ligada a domínios para se ligar às suas máquinas. O laboratório também suporta o uso de autenticação simbólica na máquina de gateway que permite aos utilizadores conectarem-se às suas máquinas virtuais de laboratório sem terem a porta RDP exposta à internet. Este artigo percorre um exemplo sobre como criar um laboratório que usa a autenticação simbólica para se ligar a máquinas de laboratório.

## <a name="architecture-of-the-solution"></a>Arquitetura da solução

![Arquitetura da solução](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. A ação [do conteúdo do ficheiro Get RDP](/rest/api/dtl/virtualmachines/getrdpfilecontents) é chamada quando seleciona o botão Ligar.1. **Connect** 
1. A ação de conteúdo de ficheiro Get RDP invoca `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` para solicitar um token de autenticação.
    1. `{gateway-hostname}` é o nome de anfitrião de gateway especificado na página **de Definições** de Laboratório para o seu laboratório no portal Azure. 
    1. `{lab-machine-name}` é o nome da máquina que está a tentar ligar.
    1. `{port-number}` é a porta sobre a qual a ligação precisa de ser feita. Normalmente esta porta é 3389. Se o laboratório VM estiver a usar a funcionalidade [IP partilhada](devtest-lab-shared-ip.md) nos Laboratórios DevTest, a porta será diferente.
1. O gateway de ambiente de trabalho remoto adia a chamada `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` de uma função Azure para gerar o token de autenticação. O serviço DevTest Labs inclui automaticamente a chave de função no cabeçalho de pedido. A chave de função deve ser guardada no cofre do laboratório. O nome desse segredo será mostrado como **segredo de Gateway** na página de **Definições** de Laboratório para o laboratório.
1. Espera-se que a função Azure devolva um símbolo para autenticação simbólica baseada em certificados contra a máquina de gateway.  
1. A ação de conteúdo de ficheiro Get RDP devolve então o ficheiro RDP completo, incluindo as informações de autenticação.
1. Abra o ficheiro RDP utilizando o seu programa de ligação RDP preferido. Lembre-se que nem todos os programas de ligação RDP suportam a autenticação simbólica. O token de autenticação tem uma data de validade, definida pela aplicação de função. Faça a ligação com o VM do laboratório antes que o símbolo expire.
1. Uma vez que a máquina de gateway de secretária remota autentica o símbolo no ficheiro RDP, a ligação é reencaminhada para a sua máquina de laboratório.

### <a name="solution-requirements"></a>Requisitos da solução
Para trabalhar com a funcionalidade de autenticação simbólica DevTest Labs, existem alguns requisitos de configuração para as máquinas de gateway, serviços de nome de domínio (DNS) e funções.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Requisitos para máquinas de gateway de ambiente de trabalho remoto
- O certificado TLS/SSL deve ser instalado na máquina de porta de entrada para manusear o tráfego HTTPS. O certificado deve corresponder ao nome de domínio totalmente qualificado (FQDN) do equilibrador de carga para a fazenda gateway ou o FQDN da própria máquina se houver apenas uma máquina. Os certificados de wild-card TLS/SSL não funcionam.  
- Um certificado de assinatura instalado na máquina de gateway(s). Crie um certificado de assinatura utilizando [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) script.
- Instale o módulo [de autenticação pluggável](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) que suporta a autenticação simbólica para o gateway remoto do ambiente de trabalho. Um exemplo destes `RDGatewayFedAuth.msi` módulos é que vem com [imagens do System Center Virtual Machine Manager (VMM).](/system-center/vmm/install-console?view=sc-vmm-1807) Para obter mais informações sobre o System Center, consulte [a documentação](/system-center/) do System Center e [detalhes sobre os preços.](https://www.microsoft.com/cloud-platform/system-center-pricing)  
- O servidor gateway pode lidar com os pedidos feitos para `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` .

    O nome de gateway-hostname é o FQDN do equilibrador de carga da fazenda gateway ou o FQDN da própria máquina se houver apenas uma máquina. É `{lab-machine-name}` o nome da máquina de laboratório que está a tentar ligar, e a `{port-number}` porta é a porta na qual a ligação será feita.  Por predefinição, esta porta é 3389.  No entanto, se a máquina virtual estiver a utilizar a funcionalidade [IP partilhada](devtest-lab-shared-ip.md) nos Laboratórios DevTest, a porta será diferente.
- O módulo [de Pedido de Encaminhamento de Aplicações](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) para o Servidor de Informações da Internet (IIS) pode ser utilizado para redirecionar `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` pedidos para a função azul, que trata do pedido de obter um símbolo para autenticação.


## <a name="requirements-for-azure-function"></a>Requisitos para a função Azure
A função Azure trata do pedido com formato de e devolve o token de `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` autenticação com base no mesmo certificado de assinatura instalado nas máquinas de gateway. O `{function-app-uri}` uri usado para aceder à função. A tecla de função é automaticamente transmitida no cabeçalho do pedido. Para uma função de amostra, consulte [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs) . 


## <a name="requirements-for-network"></a>Requisitos para rede

- O DNS para a FQDN associado ao certificado TLS/SSL instalado nas máquinas de gateway deve direcionar o tráfego para a máquina de gateway ou para o equilibrador de carga da exploração da máquina de gateway.
- Se a máquina de laboratório utilizar IPs privados, deve haver um caminho de rede desde a máquina de gateway até à máquina de laboratório, seja através da partilha da mesma rede virtual ou da utilização de redes virtuais.

## <a name="configure-the-lab-to-use-token-authentication"></a>Configure o laboratório para usar a autenticação simbólica 
Esta secção mostra como configurar um laboratório para usar uma máquina de gateway remota que suporta a autenticação simbólica. Esta secção não cobre como configurar uma quinta remota de gateways de secretária. Para obter esta informação, consulte a Amostra para criar uma secção [de gateway de secretária remota](#sample-to-create-a-remote-desktop-gateway) no final deste artigo. 

Antes de atualizar as definições do laboratório, guarde a chave necessária para executar com sucesso a função para devolver um token de autenticação no cofre do laboratório. Pode obter o valor chave de função na página **'Gerir'** para a função no portal Azure. Para obter mais informações sobre como guardar um segredo num cofre de chaves, consulte [Adicionar um segredo ao Key Vault.](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) Guarde o nome do segredo para uso posterior.

Para encontrar a ID do cofre chave do laboratório, corra o seguinte comando Azure CLI: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Configure o laboratório para utilizar a autenticação simbólica utilizando estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Da lista de laboratórios, selecione o seu **laboratório.**
1. Na página do laboratório, selecione **Configuração e políticas**.
1. No menu esquerdo, na secção **Definições,** selecione **as definições do Laboratório**.
1. Na secção **de ambiente de trabalho remoto,** insira o nome de domínio totalmente qualificado (FQDN) ou endereço IP da máquina de gateway de serviços de ambiente remoto ou fazenda para o campo de nome de **anfitrião Gateway** Gateway. Este valor deve corresponder ao FQDN do certificado TLS/SSL utilizado nas máquinas de gateway.

    ![Opções de ambiente de trabalho remoto nas definições de laboratório](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. Na secção **de ambiente de trabalho remoto,** para o segredo de **Gateway,** insira o nome do segredo criado anteriormente. Este valor não é a chave da função em si, mas o nome do segredo no cofre chave do laboratório que detém a chave de função.

    ![Gateway token segredo em configurações de laboratório](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Salvar** As mudanças.

    > [!NOTE] 
    > Ao clicar em **Guardar**, concorda com os [termos de licença do Remote Desktop Gateway](https://www.microsoft.com/licensing/product-licensing/products). Para obter mais informações sobre o gateway remoto, consulte [Welcome to Remote Desktop Services](/windows-server/remote/remote-desktop-services/Welcome-to-rds) e [Implemente o seu ambiente de trabalho remoto](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Se a configuração do laboratório através da automatização for preferível, consulte [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) para uma amostra do script PowerShell para definir **o nome de anfitrião gateway** e as definições **secretas de gateway token.** O [repositório GitHub do Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) também fornece um modelo de Gestor de Recursos Azure que cria ou atualiza um laboratório com o **nome de anfitrião** gateway e as definições **secretas de porta de entrada.**

## <a name="configure-network-security-group"></a>Configure grupo de segurança de rede
Para proteger ainda mais o laboratório, um grupo de segurança de rede (NSG) pode ser adicionado à rede virtual usada pelas máquinas virtuais do laboratório. Para obter instruções sobre como configurar um NSG, consulte [Criar, alterar ou eliminar um grupo de segurança de rede](../virtual-network/manage-network-security-group.md).

Aqui está um exemplo NSG que só permite o tráfego que primeiro passa pela porta de entrada para chegar a máquinas de laboratório. A fonte desta regra é o endereço IP da máquina de gateway única, ou o endereço IP do equilibrador de carga em frente às máquinas de gateway.

![Grupo de segurança de rede - regras](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Amostra para criar um gateway remoto de desktop

> [!NOTE] 
> Ao utilizar os modelos de amostra, concorda com os [termos de licença do Remote Desktop Gateway](https://www.microsoft.com/licensing/product-licensing/products). Para obter mais informações sobre o gateway remoto, consulte [Welcome to Remote Desktop Services](/windows-server/remote/remote-desktop-services/Welcome-to-rds) e [Implemente o seu ambiente de trabalho remoto](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

O [repositório GitHub do Azure DevTest Labs fornece algumas amostras](https://github.com/Azure/azure-devtestlab) para ajudar a configurar os recursos necessários para usar a autenticação simbólica e o gateway remoto de desktop com a DevTest Labs. Estas amostras incluem modelos de Gestor de Recursos Azure para máquinas de gateway, configurações de laboratório e aplicação de função.

Siga estes passos para configurar uma solução de amostra para a exploração remota do gateway de secretária.

1. Crie um certificado de assinatura.  Executar [Create-SigningCertificate.ps1. ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) Guarde a impressão digital, a palavra-passe e a codificação base64 do certificado criado.
2. Obtenha um certificado TLS/SSL. A FQDN associada ao certificado TLS/SSL deve ser para o domínio que controla. Guarde a impressão digital, a palavra-passe e a codificação base64 para este certificado. Para obter a impressão digital utilizando o PowerShell, utilize os seguintes comandos.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Para obter a codificação Base64 utilizando o PowerShell, utilize o seguinte comando.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Descarregue ficheiros a partir de [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) .

    O modelo requer acesso a alguns outros modelos de Gestor de Recursos e recursos relacionados na mesma base URI. Copie todos os ficheiros de [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) e RDGatewayFedAuth.msi para um recipiente de bolhas numa conta de armazenamento.  
4. Implemente **azuredeploy.jsa** partir de [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) . O modelo toma os seguintes parâmetros:
    - adminUsername – Obrigatório.  Nome de utilizador do administrador para as máquinas de gateway.
    - adminPassword – Obrigatório. Palavra-passe para a conta do administrador para as máquinas de gateway.
    - instânciaCount – Número de máquinas de gateway para criar.  
    - alwaysOn – Indica se deve manter a aplicação Azure Functions criada em estado quente ou não. Manter a aplicação Azure Functions evitará atrasos quando os utilizadores tentam ligar-se pela primeira vez ao seu VM de laboratório, mas tem implicações de custos.  
    - tokenLifetime – O tempo de tempo que o token criado será válido. O formato é HH:MM:SS.
    - sslCertificate – A codificação base64 do certificado TLS/SSL para a máquina de gateway.
    - sslCertificatePassword – A palavra-passe do certificado TLS/SSL para a máquina de gateway.
    - sslCertificateThumbprint - A impressão digital do certificado para identificação na loja de certificados local do certificado TLS/SSL.
    - signCertificato – A codificação base64 para o certificado de assinatura da máquina de gateway.
    - signCertificatePassword – A palavra-passe para o certificado de assinatura para a máquina de gateway.
    - signCertificateThumbprint - A impressão digital do certificado para identificação na loja de certificados local do certificado de assinatura.
    - _artifactsLocation – localização URI onde todos os recursos de apoio podem ser encontrados. Este valor deve ser um UIR totalmente qualificado, não um caminho relativo.
    - _artifactsLocationSasToken – O símbolo de Assinatura de Acesso Partilhado (SAS) usado para aceder a recursos de suporte, se a localização for uma conta de armazenamento Azure.

    O modelo pode ser implantado utilizando o CLI Azure utilizando o seguinte comando:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Aqui estão as descrições dos parâmetros:

    - O {storage-account-endpoint} pode ser obtido executando `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob` .  O nome {storage-acct} é o nome da conta de armazenamento que contém ficheiros que fez o upload.  
    - O {container-name} é o nome do recipiente no {storage-acct-name} que contém ficheiros que fez o upload.  
    - O {sas-token} pode ser obtido executando `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}` . 
        - O nome {storage-acct} é o nome da conta de armazenamento que contém ficheiros que fez o upload.  
        - O {container-name} é o nome do recipiente no {storage-acct-name} que contém ficheiros que fez o upload.  
        - A data de validade {utc-expiração} é a data, na UTC, na qual o token SAS expirará e o token SAS já não pode ser usado para aceder à conta de armazenamento.

    Registar os valores para gatewayFQDN e gatewayIP a partir da saída de implementação do modelo. Também terá de guardar o valor da chave de função para a função recém-criada, que pode ser encontrada no separador definições de [aplicações 'Função'.](../azure-functions/functions-how-to-use-azure-function-app-settings.md)
5. Configure o DNS de modo a que a FQDN do TLS/SSL cert direcione para o endereço IP do gatewayIP a partir do passo anterior.

    Depois de a fazenda Remote Desktop Gateway ser criada e forem feitas atualizações apropriadas de DNS, está pronta para ser usada por um laboratório em DevTest Labs. O **nome de anfitrião do gateway** e as definições **secretas simbólicas** de gateway devem ser configuradas para utilizar a ou as máquinas de porta de entrada que implementou. 

    > [!NOTE]
    > Se a máquina de laboratório utilizar IPs privados, deve haver um caminho de rede desde a máquina de gateway até à máquina de laboratório, seja através da partilha da mesma rede virtual ou através de uma rede virtual esprevada.

    Uma vez configurados tanto o gateway como o laboratório, o ficheiro de ligação criado quando o utilizador do laboratório clica no **Connect** incluirá automaticamente as informações necessárias para se ligar utilizando a autenticação simbólica.     

## <a name="next-steps"></a>Passos seguintes
Consulte o seguinte artigo para saber mais sobre Serviços remotos de desktop: [Documentação de Serviços de Ambiente de Trabalho Remoto](/windows-server/remote/remote-desktop-services/Welcome-to-rds)