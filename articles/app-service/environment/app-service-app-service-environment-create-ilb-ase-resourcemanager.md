---
title: Criar o ILB ASE utilizando modelos Azure Resource Manager - serviço de aplicações | Documentos da Microsoft
description: Saiba como criar um ASE de Balanceador de carga interno utilizando modelos Azure Resource Manager.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 35e0dc5dabaf1602b87ec6a8be86ed609f3ea12f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130760"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Como Criar um ASE do ILB Utilizando Modelos do Azure Resource Manager

> [!NOTE] 
> Este artigo é sobre o ambiente de serviço de aplicações v1. Existe uma versão mais recente do ambiente de serviço de aplicação que é mais fácil de usar e é executado numa infraestrutura mais poderosa. Para saber mais sobre o novo início de versão com o [introdução ao ambiente de serviço de aplicações](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral
Ambientes de serviço de aplicações podem ser criados com um endereço de rede virtual interna em vez de um VIP público.  Este endereço interno é fornecido por um componente do Azure chamado o Balanceador de carga interno (ILB).  Um ASE de ILB podem ser criado no portal do Azure.  Ele também pode ser criado com a automatização por meio de modelos Azure Resource Manager.  Este artigo explica os passos e a sintaxe necessária para criar um ASE de ILB com modelos Azure Resource Manager.

Existem três passos envolvidos na Automação de criação de um ASE de ILB:

1. O ASE base é criado pela primeira vez numa rede virtual com um endereço de Balanceador de carga interno, em vez de um VIP público.  Como parte deste passo, um nome de domínio de raiz é atribuído para o ASE de ILB.
2. Depois de criar o ASE de ILB, é carregado um certificado SSL.  
3. O certificado SSL carregado está atribuído explicitamente para o ASE de ILB como seu certificado SSL de "predefinição".  Este certificado SSL será utilizado para tráfego SSL às aplicações num ASE de ILB quando as aplicações são resolvidas através do domínio de raiz comuns atribuído (por exemplo, para o ASE https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Criar a Base de ASE de ILB
Um modelo do Azure Resource Manager de exemplo e o respetivo ficheiro de parâmetros associados, estão disponíveis no GitHub [aqui][quickstartilbasecreate].

A maioria dos parâmetros nos *azuredeploy* ficheiro são comuns a criação de tanto os ASEs do ILB, bem como os ASEs vinculados a um VIP público.  A lista abaixo chama parâmetros de destacar de saída, ou que são exclusivos, ao criar um ASE de ILB:

* *internalLoadBalancingMode*:  Na maioria dos casos conjunto esta opção para 3, o que significa que o tráfego HTTP/HTTPS na portas 80/443 e o canal de controlo/dados portas abertas pelo serviço FTP sobre o ASE, sejam ligada com um ILB atribuído o endereço de rede virtual.  Se esta propriedade em vez disso, é definida como 2, apenas o serviço FTP relacionados com portas (canais de controlo e dados) sejam ligadas com um endereço ILB, enquanto o tráfego HTTP/HTTPS irá permanecer no VIP público.
* *dnsSuffix*:  Este parâmetro define o domínio de raiz predefinido que será atribuído para o ASE.  A variação pública do serviço de aplicações do Azure, o domínio de raiz predefinida para todas as web apps é *azurewebsites.net*.  No entanto, uma vez que um ASE de ILB é interno para a rede virtual de um cliente, não faz sentido usar o domínio de raiz do serviço público predefinido.  Em vez disso, um ASE de ILB deve ter um domínio de raiz predefinida que faça sentido para utilização na rede virtual interna de uma empresa.  Por exemplo, uma corporação Contoso hipotético poderá utilizar um domínio de raiz predefinida da *interna contoso.com* para aplicações que se destinam a só ser resolvível e acessível numa rede virtual da Contoso. 
* *ipSslAddressCount*:  Este parâmetro automaticamente padrão é obtido um valor de 0 no *azuredeploy. JSON* ficheiros porque os ASEs do ILB tem apenas um único endereço ILB.  Não há nenhum endereço IP SSL explícito para um ASE de ILB e, por conseguinte, o conjunto de endereços IP SSL para um ASE de ILB tem de ser definido para zero, caso contrário, ocorrerá um erro de aprovisionamento. 

Uma vez a *azuredeploy* foi preenchido ficheiro para um ASE de ILB, o ASE de ILB, em seguida, podem ser criado usando o seguinte trecho de código do Powershell.  Altere o arquivo caminhos para corresponder ao onde os arquivos de modelo do Azure Resource Manager estão localizados no seu computador.  Lembre-se também de fornecer seus próprios valores para o nome de implementação Azure Resource Manager e o nome do grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Depois do Azure Resource Manager modelo é submetido irá demorar algumas horas até que o ASE de ILB a ser criada.  Uma vez concluída a criação, o ASE de ILB serão apresentados no portal do UX na lista de ambientes de serviço de aplicações para a subscrição que disparou a implementação.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>A carregar e configurar o certificado SSL "Predefinido"
Depois de criar o ASE de ILB, um certificado SSL deve ser associado com o ASE, como o certificado SSL do "padrão" utiliza para estabelecer ligações SSL para aplicações.  Continuando com o exemplo hipotético do Contoso Corporation, se o ASE predefinido DNS é de sufixo *interna contoso.com*, em seguida, uma ligação ao *https://some-random-app.internal-contoso.com* requer um certificado SSL que é válido para o **.internal contoso.com*. 

Existem diversas formas de obter um certificado SSL válido, incluindo o ACS internas, adquirir um certificado de um emissor externo e utilizar um certificado autoassinado.  Independentemente da origem do certificado SSL, os seguintes atributos de certificado têm de ser configurados corretamente:

* *Assunto*:  Este atributo deve ser definido como **.your-raiz-domínio-here.com*
* *Nome alternativo do requerente*:  Este atributo tem de incluir **.your-raiz-domínio-here.com*, e **Here-raiz-domínio-here.com*.  O motivo para a segunda entrada é que as ligações SSL ao site SCM/Kudu associadas a cada aplicação serão efetuadas através de um endereço do formulário *your-app-name.scm.your-root-domain-here.com*.

Com um certificado SSL válido em mãos, são necessárias duas etapas preparatórias adicionais.  O certificado SSL deve ser convertido/guardado como ficheiro. pfx.  Lembre-se de que o ficheiro. pfx tem de incluir todos os intermediários e certificados de raiz e, também tem de ser protegido com uma palavra-passe.

Em seguida, o ficheiro. pfx resultante tem de ser convertido numa cadeia base64 porque o certificado SSL será carregado com um modelo Azure Resource Manager.  Uma vez que os modelos Azure Resource Manager são ficheiros de texto, o ficheiro. pfx tem de ser convertido numa cadeia base64, pelo que pode ser incluído como um parâmetro do modelo.

O trecho de código do Powershell abaixo mostra um exemplo de gerar um certificado autoassinado, exportar o certificado como um ficheiro. pfx, cadeia codificada de converter o ficheiro. pfx num base64 e, em seguida, guarde o base64 codificado a cadeia de caracteres para um arquivo separado.  O código do Powershell para a codificação base64 foi adaptado do [Blog de Scripts do Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Depois do certificado SSL foi com êxito gerado e convertido numa codificação de base64 string, o modelo do Azure Resource Manager de exemplo no GitHub para [configurar o certificado SSL predefinido] [ configuringDefaultSSLCertificate] pode ser utilizado.

Os parâmetros nos *azuredeploy* arquivo estão listados abaixo:

* *appServiceEnvironmentName*:  O nome do ASE de ILB a ser configurado.
* *existingAseLocation*:  Cadeia de texto que contém a região do Azure em que o ASE de ILB foi implementado.  Por exemplo:  "Sul dos E.U.A.".
* *pfxBlobString*:  O based64 codificado representação de cadeia de caracteres do arquivo. pfx.  Usando o trecho de código mostrado anteriormente, seria copiar a cadeia de caracteres contida no "exportedcert.pfx.b64" e cole-a no como o valor do *pfxBlobString* atributo.
* *palavra-passe*:  A palavra-passe utilizada para proteger o ficheiro. pfx.
* *certificateThumbprint*:  Thumbprint do certificado.  Se recuperar esse valor a partir do Powershell (por exemplo, *$certificate. Thumbprint* do trecho de código anterior), pode usar o valor como-é.  No entanto se copiar o valor na caixa de diálogo de certificado do Windows, não se esqueça de retirar os espaços estranhos.  O *certificateThumbprint* deve ser algo semelhante:  AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*:  Utilizado um identificador de cadeia de caracteres amigável seus próprios escolher para identidade o certificado.  O nome é utilizado como parte do identificador exclusivo do Azure Resource Manager para o *Microsoft.Web/certificates* entidade que representa o certificado SSL.  O nome **tem** terminar com o seguinte sufixo: \_yourASENameHere_InternalLoadBalancingASE.  Este sufixo é utilizado pelo portal como um indicador de que o certificado é utilizado para proteger um ASE com ILB ativado.

Um exemplo abreviado *azuredeploy* é mostrado abaixo:

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

Uma vez a *azuredeploy* ficheiro tiver sido preenchido, o certificado SSL predefinido pode ser configurado com o seguinte trecho de código do Powershell.  Altere o arquivo caminhos para corresponder ao onde os arquivos de modelo do Azure Resource Manager estão localizados no seu computador.  Lembre-se também de fornecer seus próprios valores para o nome de implementação Azure Resource Manager e o nome do grupo de recursos.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Depois do Azure Resource Manager modelo é submetido irá demorar aproximadamente quarenta minutos por ASE front-end para aplicar a alteração.  Por exemplo, com um ASE de predefinição em tamanho normal utilizando dois front-ends, o modelo irá demorar aproximadamente uma hora e vinte minutos a concluir.  Enquanto o modelo está em execução o ASE não será capaz de dimensionada.  

Assim que o modelo for concluída, aplicações num ASE de ILB podem ser acedidas através de HTTPS e as ligações esteja protegidas com o certificado SSL predefinido.  O certificado SSL predefinido será utilizado quando as aplicações no ASE de ILB são resolvidas utilizando uma combinação de nome de anfitrião predefinido além disso, o nome da aplicação.  Por exemplo *https://mycustomapp.internal-contoso.com* utilizaria o certificado SSL predefinido para **.internal contoso.com*.

No entanto, assim como aplicações em execução no serviço de multi-inquilino público, os desenvolvedores podem também configurar nomes de anfitrião personalizado para aplicações individuais e, em seguida, configurar os enlaces de certificado de SNI SSL exclusivos para aplicações individuais.  

## <a name="getting-started"></a>Introdução
Para começar a utilizar com ambientes de serviço de aplicações, veja [introdução ao ambiente de serviço de aplicações](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

