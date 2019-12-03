---
title: Criar ILB ASE v1
description: Crie um ambiente do serviço de aplicativo com um balanceador de carga interno (ILB ASE). Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 9cbd8b178bfd2edcf99e3bba9b0d967aebcb5cc2
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688781"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Como Criar um ASE do ILB Utilizando Modelos do Azure Resource Manager

> [!NOTE] 
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1. Há uma versão mais recente do Ambiente do Serviço de Aplicativo que é mais fácil de usar e é executada em uma infraestrutura mais potente. Para saber mais sobre a nova versão, comece com a [introdução ao ambiente do serviço de aplicativo](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral
Ambientes de serviço de aplicativo podem ser criados com um endereço interno de rede virtual em vez de um VIP público.  Esse endereço interno é fornecido por um componente do Azure chamado ILB (balanceador de carga interno).  Um ASE ILB pode ser criado usando o portal do Azure.  Ele também pode ser criado usando a automação por meio de modelos de Azure Resource Manager.  Este artigo percorre as etapas e a sintaxe necessárias para criar um ASE ILB com modelos de Azure Resource Manager.

Há três etapas envolvidas na automação da criação de um ASE ILB:

1. Primeiro, o ASE base é criado em uma rede virtual usando um endereço de balanceador de carga interno em vez de um VIP público.  Como parte desta etapa, um nome de domínio raiz é atribuído ao ASE ILB.
2. Depois que o ASE ILB é criado, um certificado SSL é carregado.  
3. O certificado SSL carregado é explicitamente atribuído ao ASE ILB como seu certificado SSL "padrão".  Esse certificado SSL será usado para o tráfego SSL para aplicativos no ASE ILB quando os aplicativos forem endereçados usando o domínio raiz comum atribuído ao ASE (por exemplo, https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Criando o ASE ILB base
Um exemplo Azure Resource Manager modelo e seu arquivo de parâmetros associado estão disponíveis no GitHub [aqui][quickstartilbasecreate].

A maioria dos parâmetros no arquivo *azuredeploy. Parameters. JSON* é comum para criar ILB ases, bem como ases associado a um VIP público.  A lista a seguir chama parâmetros de observação especial ou que são exclusivos ao criar um ASE ILB:

* *internalLoadBalancingMode*: na maioria dos casos defina isso como 3, o que significa que o tráfego http/https nas portas 80/443 e as portas de canal de dados/controle escutadas pelo serviço FTP no ase serão associadas a um endereço interno de rede virtual alocada ILB.  Se essa propriedade for definida como 2, somente as portas relacionadas ao serviço FTP (canais de controle e de dados) serão associadas a um endereço ILB, enquanto o tráfego HTTP/HTTPS permanecerá no VIP público.
* *DnsSuffix*: esse parâmetro define o domínio raiz padrão que será atribuído ao ASE.  Na variação pública do serviço de Azure App, o domínio raiz padrão para todos os aplicativos Web é *azurewebsites.net*.  No entanto, como um ASE ILB é interno à rede virtual de um cliente, não faz sentido usar o domínio raiz padrão do serviço público.  Em vez disso, um ASE ILB deve ter um domínio raiz padrão que faça sentido para uso na rede virtual interna de uma empresa.  Por exemplo, uma empresa hipotética da Contoso pode usar um domínio raiz padrão de *Internal-contoso.com* para aplicativos que devem ser resolvidos e acessíveis somente na rede virtual da contoso. 
* *ipSslAddressCount*: esse parâmetro é automaticamente padronizado para um valor de 0 no arquivo *azuredeploy. JSON* porque ILB ases tem apenas um único endereço ILB.  Não há nenhum endereço IP SSL explícito para um ASE ILB e, portanto, o pool de endereços IP-SSL para um ASE ILB deve ser definido como zero; caso contrário, ocorrerá um erro de provisionamento. 

Depois que o arquivo *azuredeploy. Parameters. JSON* tiver sido preenchido para um ase ILB, o ase ILB poderá ser criado usando o seguinte trecho de código do PowerShell.  Altere os caminhos de arquivo para corresponder onde os arquivos de modelo de Azure Resource Manager estão localizados em seu computador.  Lembre-se também de fornecer seus próprios valores para o nome da implantação do Azure Resource Manager e o nome do grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Depois que o modelo de Azure Resource Manager for enviado, levará algumas horas para que o ASE ILB seja criado.  Quando a criação for concluída, o ASE ILB aparecerá no portal UX na lista de ambientes de serviço de aplicativo para a assinatura que disparou a implantação.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Carregando e Configurando o certificado SSL "padrão"
Depois que o ASE ILB é criado, um certificado SSL deve ser associado ao ASE como o certificado SSL "padrão" usado para estabelecer conexões SSL com aplicativos.  Continuando com o exemplo hipotético da Contoso Corporation, se o sufixo DNS padrão do ASE for *Internal-contoso.com*, uma conexão com *https://some-random-app.internal-contoso.com* exigirá um certificado SSL válido para * *. Internal-contoso.com*. 

Há várias maneiras de obter um certificado SSL válido, incluindo autoridades de certificação internas, comprar um certificado de um emissor externo e usar um certificado autoassinado.  Independentemente da origem do certificado SSL, os seguintes atributos de certificado têm de ser configurados corretamente:

* *Assunto*: esse atributo deve ser definido como * *. your-root-Domain-here.com*
* *Nome alternativo da entidade*: esse atributo deve incluir * *. your-root-Domain-here.com*e * *. SCM.your-root-Domain-here.com*.  O motivo da segunda entrada é que as conexões SSL com o site SCM/kudu associado a cada aplicativo serão feitas usando um endereço do formulário *Your-app-Name.SCM.your-root-Domain-here.com*.

Com um certificado SSL válido em mãos, são necessárias duas etapas preparatórias adicionais.  O certificado SSL precisa ser convertido/salvo como um arquivo. pfx.  Lembre-se de que o arquivo. pfx precisa incluir todos os certificados intermediários e raiz e também precisa ser protegido com uma senha.

Em seguida, o arquivo. pfx resultante precisa ser convertido em uma cadeia de caracteres Base64 porque o certificado SSL será carregado usando um modelo de Azure Resource Manager.  Como os modelos de Azure Resource Manager são arquivos de texto, o arquivo. pfx precisa ser convertido em uma cadeia de caracteres Base64 para que possa ser incluído como um parâmetro do modelo.

O trecho de código do PowerShell abaixo mostra um exemplo de como gerar um certificado autoassinado, exportando o certificado como um arquivo. pfx, convertendo o arquivo. pfx em uma cadeia de caracteres codificada em Base64 e salvando a cadeia de caracteres codificada em base64 em um arquivo separado.  O código do PowerShell para codificação Base64 foi adaptado do [blog scripts do PowerShell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Depois que o certificado SSL tiver sido gerado e convertido com êxito em uma cadeia de caracteres codificada em base64, o exemplo Azure Resource Manager modelo no GitHub para [Configurar o certificado SSL padrão][configuringDefaultSSLCertificate] poderá ser usado.

Os parâmetros no arquivo *azuredeploy. Parameters. JSON* são listados abaixo:

* *appServiceEnvironmentName*: o nome do ase ILB que está sendo configurado.
* *existingAseLocation*: cadeia de texto que contém a região do Azure onde o ase ILB foi implantado.  Por exemplo: "Sul EUA Central".
* *pfxBlobString*: a representação de cadeia de caracteres codificada caracteres com do arquivo. pfx.  Usando o trecho de código mostrado anteriormente, copie a cadeia de caracteres contida em "ExportedCert. pfx. B64" e cole-a como o valor do atributo *pfxBlobString* .
* *senha*: a senha usada para proteger o arquivo. pfx.
* *certificateThumbprint*: a impressão digital do certificado.  Se você recuperar esse valor do PowerShell (por exemplo, *$Certificate. Impressão digital* do trecho de código anterior), você pode usar o valor como está.  No entanto, se você copiar o valor da caixa de diálogo certificado do Windows, lembre-se de remover os espaços estranhos.  O *certificateThumbprint* deve ser semelhante a: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *CertificateName*: um identificador de cadeia de caracteres amigável de sua escolha usada para identificar o certificado.  O nome é usado como parte do identificador de Azure Resource Manager exclusivo para a entidade *Microsoft. Web/Certificates* que representa o certificado SSL.  O nome **deve** terminar com o seguinte sufixo: \_yourASENameHere_InternalLoadBalancingASE.  Esse sufixo é usado pelo portal como um indicador de que o certificado é usado para proteger um ASE habilitado para ILB.

Um exemplo abreviado de *azuredeploy. Parameters. JSON* é mostrado abaixo:

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

Depois que o arquivo *azuredeploy. Parameters. JSON* tiver sido preenchido, o certificado SSL padrão poderá ser configurado usando o seguinte trecho de código do PowerShell.  Altere os caminhos de arquivo para corresponder onde os arquivos de modelo de Azure Resource Manager estão localizados em seu computador.  Lembre-se também de fornecer seus próprios valores para o nome da implantação do Azure Resource Manager e o nome do grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Depois que o modelo de Azure Resource Manager for enviado, levará aproximadamente 40 minutos por front-end do ASE para aplicar a alteração.  Por exemplo, com um ASE dimensionado padrão usando dois front-ends, o modelo levará cerca de uma hora e vinte minutos para ser concluído.  Enquanto o modelo estiver em execução, o ASE não poderá ser dimensionado.  

Depois que o modelo for concluído, os aplicativos no ASE ILB poderão ser acessados por HTTPS e as conexões serão protegidas usando o certificado SSL padrão.  O certificado SSL padrão será usado quando os aplicativos no ASE ILB forem resolvidos usando uma combinação do nome do aplicativo mais o hostname padrão.  Por exemplo *https://mycustomapp.internal-contoso.com* usaria o certificado SSL padrão para * *. Internal-contoso.com*.

No entanto, assim como os aplicativos em execução no serviço público de vários locatários, os desenvolvedores também podem configurar nomes de host personalizados para aplicativos individuais e, em seguida, configurar associações de certificado SSL SNI exclusivas para aplicativos individuais.  

## <a name="getting-started"></a>Introdução
Para começar a usar os ambientes do serviço de aplicativo, consulte [introdução ao ambiente do serviço de aplicativo](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

