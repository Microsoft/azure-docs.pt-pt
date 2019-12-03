---
title: Criar um ASE com ARM
description: Saiba como criar um ambiente de serviço de aplicativo ILB ou externo usando um modelo de Azure Resource Manager.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0dccefa47789b4658a7bca828b5a820db0d448e5
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688660"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Criar um ASE usando um modelo de Azure Resource Manager

## <a name="overview"></a>Visão geral

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Os ASEs (ambientes de serviço Azure App) podem ser criados com um ponto de extremidade acessível pela Internet ou um ponto de extremidade em um endereço interno em uma VNet (rede virtual) do Azure. Quando criado com um ponto de extremidade interno, esse ponto de extremidade é fornecido por um componente do Azure chamado ILB (balanceador de carga interno). O ASE em um endereço IP interno é chamado de ASE ILB. O ASE com um ponto de extremidade público é chamado de ASE externo. 

Um ASE pode ser criado usando o portal do Azure ou um modelo de Azure Resource Manager. Este artigo explica as etapas e a sintaxe de que você precisa para criar um ase externo ou um ASE ILB com modelos do Resource Manager. Para saber como criar um ASE no portal do Azure, consulte [fazer um ase externo][MakeExternalASE] ou [fazer um ase ILB][MakeILBASE].

Ao criar um ASE no portal do Azure, você pode criar sua VNet ao mesmo tempo ou escolher uma VNet preexistente para implantar no. Ao criar um ASE a partir de um modelo, você deve começar com: 

* Uma VNet do Resource Manager.
* Uma sub-rede nessa VNet. Recomendamos um tamanho de sub-rede ASE de `/24` com 256 endereços para acomodar necessidades futuras de crescimento e dimensionamento. Depois que o ASE for criado, você não poderá alterar o tamanho.
* A ID de recurso da sua VNet. Você pode obter essas informações do portal do Azure em suas propriedades de rede virtual.
* A assinatura na qual você deseja implantar.
* O local no qual você deseja implantar.

Para automatizar a criação do ASE:

1. Crie o ASE a partir de um modelo. Se você criar um ASE externo, terminará após esta etapa. Se você criar um ASE ILB, haverá mais algumas coisas a fazer.

2. Depois que o ASE ILB for criado, um certificado SSL que corresponda ao domínio do ASE ILB será carregado.

3. O certificado SSL carregado é atribuído ao ASE ILB como seu certificado SSL "padrão".  Esse certificado é usado para tráfego SSL para aplicativos no ASE ILB quando eles usam o domínio raiz comum que é atribuído ao ASE (por exemplo, https://someapp.mycustomrootdomain.com).


## <a name="create-the-ase"></a>Criar o ASE
Um modelo do Resource Manager que cria um ASE e seu arquivo de parâmetros associado está disponível [em um exemplo][quickstartasev2create] no github.

Se você quiser fazer um ASE ILB, use estes [exemplos][quickstartilbasecreate]de modelo do Resource Manager. Eles atendem a esse caso de uso. A maioria dos parâmetros no arquivo *azuredeploy. Parameters. JSON* é comum à criação de ILB ASEs e ases externa. A lista a seguir chama parâmetros de observação especial, ou que são exclusivos, quando você cria um ASE ILB:

* *internalLoadBalancingMode*: na maioria dos casos, defina como 3, o que significa que o tráfego http/https nas portas 80/443 e as portas de canal de dados/controle atendidas pelo serviço FTP no ase serão associados a um endereço interno de rede virtual alocada ILB. Se essa propriedade for definida como 2, somente as portas relacionadas ao serviço FTP (canais de controle e de dados) serão associadas a um endereço ILB. O tráfego HTTP/HTTPS permanece no VIP público.
* *DnsSuffix*: esse parâmetro define o domínio raiz padrão atribuído ao ASE. Na variação pública do serviço de Azure App, o domínio raiz padrão para todos os aplicativos Web é *azurewebsites.net*. Como um ASE ILB é interno à rede virtual de um cliente, não faz sentido usar o domínio raiz padrão do serviço público. Em vez disso, um ASE ILB deve ter um domínio raiz padrão que faça sentido para uso na rede virtual interna de uma empresa. Por exemplo, a Contoso Corporation pode usar um domínio raiz padrão de *Internal-contoso.com* para aplicativos que devem ser resolvidos e acessíveis somente na rede virtual da contoso. 
* *ipSslAddressCount*: esse parâmetro usa automaticamente como padrão o valor 0 no arquivo *azuredeploy. JSON* porque ILB ases tem apenas um único endereço ILB. Não há nenhum endereço IP SSL explícito para um ASE ILB. Portanto, o pool de endereços IP-SSL para um ASE ILB deve ser definido como zero. Caso contrário, ocorrerá um erro de provisionamento. 

Depois que o arquivo *azuredeploy. Parameters. JSON* for preenchido, crie o ase usando o trecho de código do PowerShell. Altere os caminhos de arquivo para que correspondam aos locais do arquivo de modelo do Resource Manager em seu computador. Lembre-se de fornecer seus próprios valores para o nome de implantação do Resource Manager e o nome do grupo de recursos:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Leva cerca de uma hora para que o ASE seja criado. Em seguida, o ASE aparece no portal na lista de ASEs para a assinatura que disparou a implantação.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Carregar e configurar o certificado SSL "padrão"
Um certificado SSL deve ser associado ao ASE como o certificado SSL "padrão" que é usado para estabelecer conexões SSL com aplicativos. Se o sufixo DNS padrão do ASE for *Internal-contoso.com*, uma conexão com https://some-random-app.internal-contoso.com exigirá um certificado SSL válido para * *. Internal-contoso.com*. 

Obtenha um certificado SSL válido usando autoridades de certificação internas, comprando um certificado de um emissor externo ou usando um certificado autoassinado. Independentemente da origem do certificado SSL, os seguintes atributos de certificado devem ser configurados corretamente:

* **Assunto**: esse atributo deve ser definido como * *. your-root-Domain-here.com*.
* **Nome alternativo da entidade**: esse atributo deve incluir * *. your-root-Domain-here.com* e * *. SCM.your-root-Domain-here.com*. As conexões SSL com o site SCM/kudu associado a cada aplicativo usam um endereço no formato *Your-app-Name.SCM.your-root-Domain-here.com*.

Com um certificado SSL válido em mãos, são necessárias duas etapas preparatórias adicionais. Converta/guarde o certificado SSL como um ficheiro .pfx. Lembre-se de que o arquivo. pfx deve incluir todos os certificados intermediários e raiz. Proteja-o com uma palavra-passe.

O arquivo. pfx precisa ser convertido em uma cadeia de caracteres Base64 porque o certificado SSL é carregado usando um modelo do Resource Manager. Como os modelos do Resource Manager são arquivos de texto, o arquivo. pfx deve ser convertido em uma cadeia de caracteres base64. Dessa forma, ele pode ser incluído como um parâmetro do modelo.

Use o seguinte trecho de código do PowerShell para:

* Gere um certificado autoassinado.
* Exporte o certificado como um arquivo. pfx.
* Converta o arquivo. pfx em uma cadeia de caracteres codificada em base64.
* Salve a cadeia de caracteres codificada em base64 em um arquivo separado. 

Este código do PowerShell para codificação Base64 foi adaptado do [blog scripts do PowerShell][examplebase64encoding]:

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Depois que o certificado SSL for gerado e convertido com êxito em uma cadeia de caracteres codificada em base64, use o modelo do Resource Manager de exemplo [Configurar o certificado SSL padrão][quickstartconfiguressl] no github. 

Os parâmetros no arquivo *azuredeploy. Parameters. JSON* são listados aqui:

* *appServiceEnvironmentName*: o nome do ase ILB que está sendo configurado.
* *existingAseLocation*: cadeia de texto que contém a região do Azure onde o ase ILB foi implantado.  Por exemplo: "Sul EUA Central".
* *pfxBlobString*: a representação de cadeia de caracteres codificada em caracteres com do arquivo. pfx. Use o trecho de código mostrado anteriormente e copie a cadeia de caracteres contida em "ExportedCert. pfx. B64". Cole-o como o valor do atributo *pfxBlobString* .
* *senha*: a senha usada para proteger o arquivo. pfx.
* *certificateThumbprint*: a impressão digital do certificado. Se você recuperar esse valor do PowerShell (por exemplo, *$Certificate. Impressão digital* do trecho de código anterior), você pode usar o valor como está. Se você copiar o valor da caixa de diálogo certificado do Windows, lembre-se de remover os espaços estranhos. A *certificateThumbprint* deve ser semelhante a AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *CertificateName*: um identificador de cadeia de caracteres amigável de sua escolha usada para identificar o certificado. O nome é usado como parte do identificador exclusivo do Gerenciador de recursos para a entidade *Microsoft. Web/Certificates* que representa o certificado SSL. O nome *deve* terminar com o seguinte sufixo: \_yourASENameHere_InternalLoadBalancingASE. O portal do Azure usa esse sufixo como um indicador de que o certificado é usado para proteger um ASE habilitado para ILB.

Um exemplo abreviado de *azuredeploy. Parameters. JSON* é mostrado aqui:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

Depois que o arquivo *azuredeploy. Parameters. JSON* for preenchido, configure o certificado SSL padrão usando o trecho de código do PowerShell. Altere os caminhos de arquivo para corresponder onde os arquivos de modelo do Resource Manager estão localizados em seu computador. Lembre-se de fornecer seus próprios valores para o nome de implantação do Resource Manager e o nome do grupo de recursos:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Leva aproximadamente 40 minutos por front-end do ASE para aplicar a alteração. Por exemplo, para um ASE de tamanho padrão que usa dois front-ends, o modelo leva cerca de uma hora e 20 minutos para ser concluído. Enquanto o modelo está em execução, o ASE não pode ser dimensionado.  

Após a conclusão do modelo, os aplicativos no ASE ILB podem ser acessados por HTTPS. As conexões são protegidas usando o certificado SSL padrão. O certificado SSL padrão é usado quando os aplicativos no ASE ILB são resolvidos usando uma combinação do nome do aplicativo mais o nome de host padrão. Por exemplo, https://mycustomapp.internal-contoso.com usa o certificado SSL padrão para * *. Internal-contoso.com*.

No entanto, assim como os aplicativos executados no serviço público multilocatário, os desenvolvedores podem configurar nomes de host personalizados para aplicativos individuais. Eles também podem configurar associações de certificado SSL SNI exclusivas para aplicativos individuais.

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicações v1 ##
O Ambiente de Serviço de Aplicações tem duas versões: ASEv1 e ASEv2. As informações anteriores tiveram como base a versão ASEv2. Esta secção mostra as diferenças entre as versões ASEv1 e ASEv2.

No ASEv1, você gerencia todos os recursos manualmente. Tal inclui os front-ends, os trabalhos e os endereços IP utilizados para SSL baseado em IP. Antes de poder escalar horizontalmente o plano do serviço de aplicativo, você deve escalar horizontalmente o pool de trabalho que deseja hospedá-lo.

O ASEv1 utiliza um modelo de preços diferente do ASEv2. No ASEv1, paga por cada vCPU alocada. Isso inclui vCPUs que são usados para front-ends ou trabalhadores que não hospedam nenhuma carga de trabalho. No ASEv1, o tamanho de dimensionamento máximo predefinido de um ASE é 55 anfitriões no total. Tal inclui os trabalhos e os front-ends. Uma vantagem do ASEv1 é que pode ser implementado numa rede virtual clássica e numa rede virtual do Resource Manager. Para saber mais sobre o ASEv1, consulte [introdução ao ambiente do serviço de aplicativo v1][ASEv1Intro].

Para criar um ASEv1 usando um modelo do Resource Manager, confira [criar um ase do ILB v1 com um modelo do Resource Manager][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
