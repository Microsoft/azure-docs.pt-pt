---
title: Certificados e o ambiente de serviço de aplicações - Azure
description: Explicar vários tópicos relacionados ao certificados num ASE
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ba34638bbdb838adc6f1e61b1f8b07a6915815c0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540769"
---
# <a name="certificates-and-the-app-service-environment"></a>Certificados e o ambiente de serviço de aplicações 

O Environment do serviço de aplicações é uma implementação do serviço de aplicações do Azure que é executado dentro de sua rede Virtual do Azure. Pode ser implementado com um ponto de extremidade do aplicativo acessível de internet ou de um ponto final da aplicação que está na sua VNet. Se implementar o ASE com um ponto de final de acesso de internet, essa implementação é chamada ASE externo. Se implementar o ASE com um ponto final na sua VNet, essa implementação é chamada um ASE de ILB. Pode saber mais sobre o ASE de ILB a partir da [criar e utilizar um ASE de ILB](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase) documento.

O ASE é um sistema de inquilino único. Porque se trata de inquilino único, existem algumas funcionalidades disponíveis apenas com um ASE que não estão disponíveis no multi-inquilino de serviço de aplicações. 

## <a name="ilb-ase-certificates"></a>Certificados de ASE de ILB 

Se estiver a utilizar um ASE externo, em seguida, as suas aplicações são contatadas [appname]. [asename]. p.azurewebsites.net. Por predefinição a todos os ASEs, até mesmo os ASEs do ILB, são criadas com certificados que se seguem esse formato. Quando tiver um ASE de ILB, as aplicações são atingidas com base no nome de domínio que especificou ao criar o ASE de ILB. Para as aplicações dar suporte a SSL, terá de carregar certificados. Obter um certificado SSL válido ao utilizar autoridades de certificação internas, adquirir um certificado de um emissor externo ou utilizar um certificado autoassinado. 

Existem duas opções para configurar certificados com o ASE de ILB.  Pode configurar um certificado de padrão de caráter universal para o ASE de ILB ou defina os certificados em aplicativos web individuais no ASE.  Independentemente da escolha que fizer, os seguintes atributos de certificado tem de ser configurados corretamente:

- **Assunto:** Este atributo deve ser definido como *. [your-raiz-domain-here] para um certificado de ASE de ILB com carateres universais. Se criar o certificado para a sua aplicação, em seguida, deve ser [appname]. [your-raiz-domain-here]
- **Nome alternativo do requerente:** Este atributo tem de incluir *. [your-raiz-domain-here] e SCM.{0. [your-raiz-domain-here] para o certificado de ASE de ILB com carateres universais. Se criar o certificado para a sua aplicação, em seguida, deve ser [appname]. [your-raiz-domain-here] e [appname]. SCM a seguir. [your-raiz-domain-here].

Como uma variante de terceiro, pode criar um certificado de ASE de ILB que inclui todos os nomes de aplicações individuais na SAN do certificado em vez de usar uma referência de caráter universal. O problema com este método é que precisa saber com antecedência os nomes das aplicações que estão a colocar no ASE ou terá de continuar a atualizar o certificado do ASE de ILB.

### <a name="upload-certificate-to-ilb-ase"></a>Carregar o certificado para o ASE de ILB 

Depois de um ASE de ILB é criado no portal, o certificado tem de ser definido para o ASE de ILB. Até que o certificado for definido, o ASE mostrará uma faixa que o certificado não foi definido.  

O certificado que é carregado deve ser um ficheiro. pfx. Depois do certificado é carregado, o ASE irá efetuar uma operação de dimensionamento para definir o certificado. 

Não é possível criar o ASE e carregar o certificado como uma ação no portal ou mesmo num modelo. Como uma ação separada, pode carregar o certificado utilizando um modelo, conforme descrito no [criar um ASE a partir de um modelo](./create-from-template.md) documento.  

Se pretender criar um certificado autoassinado rapidamente para fins de teste, pode utilizar o seguinte trecho do PowerShell:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
Quando criar um self assinado o certificado, terá de garantir que o nome do requerente tem o formato de CN = {ASE_NAME_HERE} _InternalLoadBalancingASE.

## <a name="application-certificates"></a>Certificados de aplicação 

Aplicações que estão alojadas num ASE podem utilizar as funcionalidades de certificado centrada em aplicações que estão disponíveis no multi-inquilino de serviço de aplicações. Esses recursos incluem:  

- Certificados SNI 
- Baseado em IP SSL, que só é suportado com um ASE externo.  Um ASE de ILB não suporta SSL baseado em IP.
- Certificados KeyVault alojado 

As instruções para carregar e gerir os certificados estão disponíveis no tutorial SSL do serviço de aplicações https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl.  Se estiver a configurar simplesmente certificados para corresponder a um nome de domínio personalizado que foram atribuídos à sua aplicação web, em seguida, essas instruções serão suficiente. Se estiver a carregar o certificado para uma aplicação web do ASE de ILB com o nome de domínio predefinido, em seguida, especifique o site do scm na SAN do certificado, conforme observado anteriormente. 

## <a name="tls-settings"></a>Definições de TLS 

Pode configurar a definição de TLS ao nível da aplicação.  

## <a name="private-client-certificate"></a>Certificado de cliente privada 

É um caso de utilização comuns configurar a sua aplicação como um cliente num modelo de cliente-servidor. Se proteger o seu servidor com um certificado de AC privado, terá de carregar o certificado de cliente para a sua aplicação.  As instruções seguintes irão carregar certificados para o truststore dos trabalhadores que a aplicação está em execução no. Se carregar o certificado a uma aplicação, pode utilizá-la com as suas outras aplicações no mesmo plano de serviço de aplicações, sem carregar o certificado novamente.

Para carregar o certificado à sua aplicação no seu ASE:

1. Gerar um *. cer* ficheiro para o seu certificado. 
2. Vá para a aplicação que precisa do certificado no portal do Azure
3. Aceda às definições de SSL na aplicação. Clique em Carregar certificado. Selecione o público. Selecione o computador Local. Forneça um nome. Procházet a vybrat sua *. cer* ficheiro. Selecione o carregamento. 
4. Copie o thumbprint.
5. Aceda às definições de aplicação. Crie um WEBSITE_LOAD_ROOT_CERTIFICATES de definição de aplicação com o thumbprint como o valor. Se tiver vários certificados, pode colocá-los na definição da mesma, separada por vírgulas e sem espaço em branco, como 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

O certificado vai estar disponível por todas as aplicações no mesmo plano de serviço de aplicações como a aplicação, que é configurado dessa definição. Se precisar de ele estar disponível para aplicações num plano do serviço de aplicação diferente, terá de repetir a operação de definição de aplicação numa aplicação nesse plano do serviço de aplicações. Para verificar que o certificado for definido, vá para a consola Kudu e emita o comando seguinte na consola de depuração do PowerShell:

    dir cert:\localmachine\root

Para realizar testes, pode criar um certificado autoassinado e gerar uma *. cer* ficheiro com o PowerShell seguinte: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

