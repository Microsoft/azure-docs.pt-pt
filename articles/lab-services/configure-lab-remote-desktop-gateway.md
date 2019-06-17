---
title: Configurar um laboratório para utilizar o Gateway de ambiente de trabalho remoto no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como configurar um laboratório no Azure DevTest Labs com um gateway de ambiente de trabalho remoto para garantir um acesso seguro ao laboratório VMs sem a necessidade de expor a porta RDP.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 430734878c01d10a4e7dd385dc75d8d502a2d82c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079005"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Configurar o laboratório no Azure DevTest Labs para utilizar um gateway de ambiente de trabalho remoto
No Azure DevTest Labs, pode configurar um gateway de ambiente de trabalho remoto para o laboratório garantir um acesso seguro para as máquinas virtuais (VMs) do laboratório sem a necessidade de expor a porta RDP. O laboratório fornece um local central para os seus utilizadores de laboratório ver e ligar a todas as máquinas virtuais que têm acesso. O **Connect** botão a **Máquina Virtual** página cria um ficheiro RDP específicas da máquina que pode abrir para ligar à máquina. Ainda pode personalizar e proteger a ligação de RDP ao ligar o seu laboratório para um gateway de ambiente de trabalho remoto. 

Essa abordagem é mais segura porque o utilizador de laboratório efetua a autenticação diretamente para a máquina de gateway ou pode utilizar as credenciais da empresa num computador associado a um domínio de gateway para ligar aos respetivos computadores. O laboratório também suporta a utilização de autenticação de token para a máquina de gateway que permite que os utilizadores ligarem às respetivas máquinas virtuais do laboratório sem ter a porta RDP exposta à internet. Este artigo explica um exemplo sobre como configurar um laboratório que utiliza a autenticação de token para ligar às máquinas de laboratório.

## <a name="architecture-of-the-solution"></a>Arquitetura da solução

![Arquitetura da solução](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. O [conteúdo do ficheiro RDP Obtenha](/rest/api/dtl/virtualmachines/getrdpfilecontents) ação denomina-se ao selecionar o **Connect** button.1. 
1. A ação de conteúdo do ficheiro RDP obter invoca `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` para pedir um token de autenticação.
    1. `{gateway-hostname}` é o nome de anfitrião de gateway especificado na **definições de laboratório** página para o laboratório no portal do Azure. 
    1. `{lab-machine-name}` é o nome da máquina que está a tentar ligar.
    1. `{port-number}` é a porta em que a conexão precisa ser feita. Normalmente, esta porta é 3389. Se o laboratório de VM está a utilizar o [partilhados IP](devtest-lab-shared-ip.md) funcionalidade no DevTest Labs, a porta será diferente.
1. O gateway de ambiente de trabalho remoto difere a chamada de `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` para uma função do Azure para gerar o token de autenticação. O serviço de DevTest Labs inclui automaticamente a tecla de função no cabeçalho do pedido. A tecla de função é guardado no Cofre de chaves do laboratório. O nome para o segredo ser mostrado como **segredo de token de Gateway** sobre o **definições de laboratório** página para o laboratório.
1. A função do Azure deverá devolver um token de autenticação de token baseada em certificados em relação a máquina de gateway.  
1. O ficheiro RDP obter conteúdo de ação, em seguida, devolve o ficheiro RDP completado, incluindo as informações de autenticação.
1. Abra o ficheiro RDP com o seu programa de ligação de RDP preferencial. Lembre-se de que nem todos os programas de ligação de RDP suportam a autenticação de token. O token de autenticação têm uma data de expiração, definido pela aplicação de função. Fazer a conexão com a VM de laboratório antes do token expira.
1. Assim que a máquina de gateway de ambiente de trabalho remoto é autenticado o token no ficheiro RDP, a ligação é reencaminhada para o seu computador de laboratório.

### <a name="solution-requirements"></a>Requisitos da solução
Para trabalhar com a funcionalidade de autenticação de token do DevTest Labs, existem alguns requisitos de configuração para funções, serviços de nome de domínio (DNS) e máquinas de gateway.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Requisitos para máquinas de gateway de ambiente de trabalho remoto
- Certificado SSL deve ser instalado no computador do gateway para processar o tráfego HTTPS. O certificado tem de corresponder ao nome de domínio completamente qualificado (FQDN) do Balanceador de carga para o farm de gateway ou o FQDN da máquina em si se houver apenas um computador. Certificados SSL de caráter universal não funcionam.  
- Um certificado de assinatura instalado na máquina de gateway (s). Criar um certificado de assinatura, utilizando [SigningCertificate.ps1 criar](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) script.
- Instalar o [autenticação conectável](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) module que suporta a autenticação de token para o gateway de ambiente de trabalho remoto. É um exemplo de um módulo de tal `RDGatewayFedAuth.msi` que acompanha [imagens do System Center Virtual Machine Manager (VMM)](/system-center/vmm/install-console?view=sc-vmm-1807). Para obter mais informações sobre o System Center, consulte [documentação do System Center](https://docs.microsoft.com/system-center/) e [os detalhes dos preços](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- O servidor de gateway pode processar pedidos efetuados a `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`.

    O gateway-nome do anfitrião é o FQDN do Balanceador de carga do farm de gateway ou o FQDN do computador, se houver apenas um computador. O `{lab-machine-name}` é o nome da máquina de laboratório que está a tentar ligar, e o `{port-number}` é a porta em que a ligação será estabelecida.  Por predefinição, esta porta é 3389.  No entanto, se a máquina virtual estiver a utilizar o [partilhados IP](devtest-lab-shared-ip.md) funcionalidade no DevTest Labs, a porta será diferente.
- O [pedido de encaminhamento de aplicação](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) módulo para o servidor de informação de Internet (IIS) pode ser utilizado para redirecionar `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` pedidos para a função do azure, que processa o pedido para obter um token para autenticação.


## <a name="requirements-for-azure-function"></a>Requisitos para a função do Azure
Pedido de identificadores de função do Azure com o formato de `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` e devolve o token de autenticação com base na mesma assinatura do certificado instalado nos computadores de gateway. O `{function-app-uri}` é o uri utilizado para aceder a função. A tecla de função é automaticamente transmitido no cabeçalho do pedido. Para uma função de exemplo, consulte [ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs). 


## <a name="requirements-for-network"></a>Requisitos de rede

- DNS para o FQDN associado ao certificado SSL instalado nos computadores de gateway tem de direcionar o tráfego para a máquina de gateway ou o Balanceador de carga do farm de máquina de gateway.
- Se o computador de laboratório utilizar IPs privados, deve haver um caminho de rede da máquina de gateway para a máquina de laboratório, por meio da mesma rede virtual de partilha ou através de redes virtuais em modo de peering.

## <a name="configure-the-lab-to-use-token-authentication"></a>Configure o laboratório para utilizar a autenticação de token 
Esta secção mostra como configurar um laboratório para utilizar uma máquina de gateway de ambiente de trabalho remoto que suporta a autenticação de token. Esta secção não abrange como configurar um farm de gateway de ambiente de trabalho remoto em si. Para obter essas informações, consulte a [exemplo para criar um gateway de ambiente de trabalho remoto](#sample-to-create-a-remote-desktop-gateway) secção no final deste artigo. 

Antes de atualizar as definições de laboratório, armazene a chave necessária para executar com êxito a função devolver um token de autenticação no Cofre de chaves do laboratório. Pode obter o valor da chave de função no **gerir** página para a função no portal do Azure. Para obter mais informações sobre como guardar um segredo no Cofre de chaves, consulte [adicionar um segredo ao Key Vault](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault). Guarde o nome do segredo para utilização posterior.

Para localizar o ID de Cofre de chaves do laboratório, execute o seguinte comando da CLI do Azure: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Configure o laboratório para utilizar a autenticação de token com estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione seu **laboratório**.
1. Na página do laboratório, selecione **Konfigurace a zásady**.
1. No menu da esquerda, na **configurações** secção, selecione **definições de laboratório**.
1. Na **ambiente de trabalho remoto** secção, introduza o nome de domínio completamente qualificado (FQDN) ou o endereço IP do computador do gateway de serviços de ambiente de trabalho remoto ou farm para o **nome de anfitrião de Gateway** campo. Este valor tem de corresponder ao FQDN do certificado SSL utilizado em máquinas de gateway.

    ![Opções de ambiente de trabalho remotas nas definições de laboratório](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. Na **ambiente de trabalho remoto** secção, para **o token de Gateway** segredo, introduza o nome do segredo criado anteriormente. Este valor não é a tecla de função em si, mas o nome do segredo do Cofre de chaves do laboratório que mantém a tecla de função.

    ![Segredo de token de gateway nas definições de laboratório](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Guardar** alterações.

    > [!NOTE] 
    > Ao clicar em **salvar**, concorda com [termos de licenciamento do Gateway Desktop remoto](https://www.microsoft.com/licensing/product-licensing/products). Para obter mais informações sobre o gateway remoto, consulte [bem-vindo dos serviços de ambiente de trabalho remoto](https://aka.ms/rds) e [implementar o seu ambiente de área de trabalho remoto](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Se configurar o laboratório através de automatização é preferencial, veja [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) para obter um script do PowerShell de exemplo definir **nome de anfitrião de gateway** e **segredo de token de gateway**as definições. O [repositório do GitHub do Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) também fornece um modelo do Azure Resource Manager que cria ou atualiza um laboratório com o **nome de anfitrião de gateway** e **segredo de token de gateway**as definições.

## <a name="configure-network-security-group"></a>Configurar o grupo de segurança de rede
Para proteger ainda mais o laboratório, um grupo de segurança de rede (NSG) pode ser adicionado à rede virtual utilizada pelas máquinas de virtuais de laboratório. Para obter instruções sobre como configurar um NSG, consulte [criação, alteração ou eliminar um grupo de segurança de rede](../virtual-network/manage-network-security-group.md).

Eis um exemplo NSG que permite apenas tráfego que passa pela primeira vez através do gateway para aceder a máquinas de laboratório. A origem nesta regra é o endereço IP do computador gateway único ou o endereço IP do Balanceador de carga à frente de máquinas do gateway.

![Grupo de segurança de rede - regras](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Exemplo para criar um gateway de ambiente de trabalho remoto

> [!NOTE] 
> Ao utilizar os modelos de exemplo, aceita [termos de licenciamento do Gateway Desktop remoto](https://www.microsoft.com/licensing/product-licensing/products). Para obter mais informações sobre o gateway remoto, consulte [bem-vindo dos serviços de ambiente de trabalho remoto](https://aka.ms/rds) e [implementar o seu ambiente de área de trabalho remoto](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

O [repositório do GitHub do Azure DevTest Labs](https://github.com/Azure/azure-devtestlab) fornece alguns exemplos para ajudar a configuração os recursos necessários para utilizar a autenticação de token e o gateway de ambiente de trabalho remoto com os laboratórios DevTest. As amostras incluem modelos Azure Resource Manager para máquinas de gateway, as definições de laboratório e aplicação de funções.

Siga estes passos para configurar uma solução de exemplo para o farm de gateway de ambiente de trabalho remoto.

1. Crie um certificado de assinatura.  Run [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Guarde o thumbprint, palavra-passe e codificação de Base64 do certificado criado.
2. Obtenha um certificado SSL. Tem de ser FQDN associado ao certificado SSL para o domínio que é controlar. Guarde o thumbprint, palavra-passe e codificação de Base64 para este certificado. Para obter o thumbprint com o PowerShell, utilize os seguintes comandos.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Para obter a codificação Base64 com o PowerShell, utilize o seguinte comando.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Transferir ficheiros a partir [ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway).

    O modelo requer acesso a alguns outros modelos do Resource Manager e os recursos relacionados com o mesmo URI de base. Copiar todos os ficheiros em [ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) e RDGatewayFedAuth.msi a um contentor de BLOBs numa conta de armazenamento.  
4. Implementar **azuredeploy. JSON** partir [ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway). O modelo precisa dos seguintes parâmetros:
    - adminUsername – necessário.  Nome de utilizador de administrador para as máquinas de gateway.
    - adminPassword – necessário. Palavra-passe da conta de administrador para as máquinas de gateway.
    - instanceCount – número de máquinas de gateway para criar.  
    - alwaysOn – indica se deve ter a aplicação de funções do Azure criada num Estado de acesso pouco frequente ou não. Manter a aplicação de funções do Azure irá evitar atrasos quando os utilizadores tentarem primeiro ligar à sua VM de laboratório, mas tem implicações de custo.  
    - tokenLifetime – o período de tempo que o token criado será válido. O formato é HH: mm:.
    - sslCertificate – Base64 a codificação do certificado SSL para a máquina de gateway.
    - sslCertificatePassword – a palavra-passe do certificado SSL para a máquina de gateway.
    - sslCertificateThumbprint - o thumbprint do certificado para fins de identificação no arquivo de certificados local do certificado SSL.
    - signCertificate – Base64 a codificação para a assinatura de certificado para a máquina de gateway.
    - signCertificatePassword – a palavra-passe para a assinatura de certificado para a máquina de gateway.
    - signCertificateThumbprint - o thumbprint do certificado para fins de identificação no arquivo de certificados local do certificado de assinatura.
    - artifactslocation – a localização do URI onde todos os recursos de suporte podem ser encontrados. Este valor tem de ser um UIR completamente qualificado, não é um caminho relativo.
    - artifactslocationsastoken – token o acesso à assinatura partilhado (SAS) utilizado para aceder a recursos de suporte, se a localização é uma conta de armazenamento do Azure.

    O modelo pode ser implementado com a CLI do Azure utilizando o seguinte comando:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation=”{storage-account-endpoint}/{container-name}” -–parameters _artifactsLocationSasToken = “?{sas-token}”
    ```

    Aqui estão as descrições dos parâmetros:

    - O {-conta-ponto final de armazenamento} pode ser obtido através da execução `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`.  O {storage-acct-name} é o nome da conta de armazenamento que contém ficheiros que carregou.  
    - {Container-name} é o nome do contentor no {storage-acct-name} ficheiros que carregou.  
    - O {-token sas} pode ser obtido através da execução `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`. 
        - O {storage-acct-name} é o nome da conta de armazenamento que contém ficheiros que carregou.  
        - {Container-name} é o nome do contentor no {storage-acct-name} ficheiros que carregou.  
        - A {utc--data de expiração} é a data, em UTC, em que o token SAS irá expirar e o token SAS já não pode ser utilizado para aceder à conta de armazenamento.

    Registe os valores para gatewayFQDN e gatewayIP da saída de implementação do modelo. Também terá de guardar o valor da chave para a função recentemente criada, o que pode ser encontrada na função da [as definições da aplicação de função](../azure-functions/functions-how-to-use-azure-function-app-settings.md) separador.
5. Configure o DNS para que o certificado FQDN de SSL direciona para o endereço IP do gatewayIP do passo anterior.

    Depois do farm de Gateway de ambiente de trabalho remoto é criado e as atualizações de DNS apropriadas são feitas, está pronto para ser utilizado por um laboratório no DevTest Labs. O **nome de anfitrião de gateway** e **segredo de token de gateway** definições devem ser configuradas para utilizar o computador ou computadores de gateway que implementou. 

    > [!NOTE]
    > Se o computador de laboratório utilizar IPs privados, deve haver um caminho de rede da máquina de gateway para a máquina de laboratório, por meio da mesma rede virtual de partilha ou utilizar uma rede virtual em modo de peering.

    Assim que o gateway e o laboratório estiverem configuradas, o ficheiro de ligação criado quando o utilizador de laboratório clica na **Connect** incluirá automaticamente as informações necessárias para ligar através da autenticação de token.     

## <a name="next-steps"></a>Passos Seguintes
Veja o seguinte artigo para saber mais sobre os serviços de ambiente de trabalho remoto: [Documentação de serviços de ambiente de trabalho remota](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


