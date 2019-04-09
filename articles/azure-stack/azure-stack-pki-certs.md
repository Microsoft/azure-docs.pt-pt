---
title: Sistemas integrados de requisitos de certificados de infraestrutura de chave pública de pilha do Azure para o Azure Stack | Documentos da Microsoft
description: Descreve os requisitos de implementação de certificados PKI de pilha do Azure para os sistemas integrados do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: d6d3cb99a55ae5eb8276391f22675a88e8b3d072
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276444"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Requisitos de certificado de infra-estrutura de chave pública do Azure Stack

O Azure Stack tem uma rede de infraestrutura públicas com acessíveis externamente endereços IP públicos atribuídos a um pequeno conjunto de serviços do Azure Stack e, possivelmente, VMs inquilinas. Certificados PKI com os nomes DNS apropriados para esses pontos de extremidade de infraestrutura públicas do Azure Stack são necessários durante a implementação do Azure Stack. Este artigo fornece informações sobre:

- Quais certificados são necessários para implementar o Azure Stack
- O processo de obtenção de certificados que correspondam essas especificações
- Como preparar, validar e utilizar esses certificados durante a implementação

> [!Note]  
> Durante a implementação tem de copiar certificados para a pasta de implementação que corresponde ao fornecedor de identidade que está a implementar em relação a (Azure AD ou AD FS). Se utilizar um único certificado para todos os pontos finais, tem de copiar esse ficheiro de certificado para cada pasta de implementação, conforme descrito nas tabelas abaixo. A estrutura de pastas baseia-se previamente na máquina de virtual de implementação e pode ser encontrada em: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Requisitos de certificado
A lista seguinte descreve os requisitos de certificados que são necessários para implementar o Azure Stack: 
- Certificados tem de ser emitidos a partir de uma autoridade de certificação interna ou uma autoridade de certificação pública. Se for utilizada uma autoridade de certificação pública, têm de ser incluído na imagem do sistema operacional base, como parte do programa autoridade de raiz fidedigna Microsoft. Pode encontrar a lista completa aqui: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- A infraestrutura do Azure Stack tem de ter acesso à rede para a localização de lista de revogação de certificados (CRL) da autoridade de certificação publicada no certificado. Esta CRL tem de ser um ponto final http
- Quando efetuar a rotação de certificados na pré-1903 baseia-se, certificados tem de ser que um emitidos pela mesma autoridade de certificado interno utilizada para assinar certificados fornecidos na implantação ou qualquer autoridade de certificação pública acima. Para certificados de 1903 & posteriores podem ser emitidos por qualquer enterprise ou de uma autoridade de certificação pública.
- A utilização de certificados autoassinados não são suportados
- Para a implementação e a rotação, pode usam um único certificado que abrange todos os espaços de nomes em campos de nome do requerente e o nome alternativo do requerente (SAN) do certificado ou pode utilizar o indivíduo certificados, para cada espaço de nomes abaixo disso, o Azure Stack exigem a serviços que pretende utilizar. Ambas as abordagens requerem a utilização de carateres universais para pontos finais, onde são necessários, tal como **KeyVault** e **KeyVaultInternal**. 
- Encriptação de PFX do certificado deve ser 3DES. 
- O algoritmo de assinatura de certificado não deve ser SHA1. 
- O formato de certificado tem de estar PFX, que as chaves públicas e privadas são necessárias para a instalação do Azure Stack. A chave privada tem de ter o atributo de chave de computador local definida.
- A encriptação de PFX tem de ser 3DES (isto é padrão ao exportar a partir de um cliente do Windows 10 ou o arquivo de certificados do Windows Server 2016).
- Os ficheiros pfx do certificado tem de ter um valor de "Assinatura Digital" e "KeyEncipherment" no seu campo de "Utilização de chave".
- Os ficheiros pfx do certificado tem de ter os valores "Autenticação de servidor (1.3.6.1.5.5.7.3.1)" e "Autenticação de cliente (1.3.6.1.5.5.7.3.2)" no campo "Utilização de chave avançada".
- O certificado "emitido para:" campo não tem de ser igual a "emitido por:" campo.
- As palavras-passe para todos os ficheiros de pfx do certificado devem ser o mesmo no momento da implementação
- Palavra-passe para o pfx do certificado tem de ser uma palavra-passe complexa. Crie uma palavra-passe que cumpra os seguintes requisitos de complexidade de palavra-passe. Um comprimento mínimo de oito caracteres. A palavra-passe contém, pelo menos, três dos seguintes: letras em maiúscula letra, letras minúsculas, números de 0 a 9, caracteres especiais, um caráter alfabético que não é maiúscula nem em minúsculas. Tome nota desta palavra-passe. Irá utilizá-lo como um parâmetro de implementação.
- Certifique-se de que os nomes de requerente e os nomes alternativos do requerente na correspondência de extensão (x509v3_config) de nome alternativo do requerente. O campo de nome alternativo do requerente permite-lhe especificar os nomes de anfitrião adicionais (os Web sites, endereços IP, nomes comuns) para ser protegido por um único certificado SSL.

> [!NOTE]  
> Auto-assinado certificados não são suportados.

> [!NOTE]  
> A presença de autoridades de certificação de intermediário no IS de cadeia de confiança de um certificado suportado. 

## <a name="mandatory-certificates"></a>Certificados obrigatórios
A tabela nesta secção descreve os certificados PKI de ponto final público do Azure Stack que são necessários para ambas do Azure AD e implementações de AD FS Azure Stack. Requisitos de certificado são agrupados por área, bem como os espaços de nomes utilizados e os certificados que são necessárias para cada espaço de nomes. A tabela também descreve a pasta em que o seu fornecedor de soluções copia os certificados diferentes por ponto final público. 

Certificados com os nomes DNS apropriados para cada ponto de extremidade de infraestrutura públicas do Azure Stack são necessários. Nome DNS de cada ponto de extremidade é expresso no formato:  *&lt;prefixo >.&lt; Região >. &lt;fqdn >*. 

Para a sua implementação, o [Região] e [externalfqdn] valores têm de corresponder a região e nomes de domínio externo que escolheu para o seu sistema do Azure Stack. Por exemplo, se o nome da região foi *Redmond* e o nome de domínio externo *contoso.com*, os nomes DNS teria o formato *&lt;prefixo >. redmond.contoso.com*. O  *&lt;prefixo >* valores são predesignated pela Microsoft para descrever o ponto final protegido pelo certificado. Além disso, o  *&lt;prefixo >* valores dos pontos finais externos infraestrutura dependem do serviço do Azure Stack que utiliza o ponto de extremidade específico. 

> [!note]  
> Para os ambientes de produção, recomendamos que certificados individuais são gerados para cada ponto de extremidade e copiados para o diretório correspondente. Para ambientes de desenvolvimento, os certificados podem ser fornecidos como um certificado único caráter universal, que abrange todos os espaços de nomes nos campos assunto e o nome alternativo do requerente (SAN) para todos os diretórios. Um único certificado abrangendo todos os pontos de extremidade e serviços é uma postura insegura, por conseguinte, apenas de desenvolvimento. Lembre-se de que ambas as opções requerem a utilização de certificados de caráter universal para pontos finais, tal como **acs** e o Key Vault onde são necessários. 

| Pasta de implementação | Assunto do certificado necessário e os nomes alternativos do requerente (SAN) | Âmbito (por região) | Espaço de nomes do subdomínio |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Portal público | portal.&lt;region>.&lt;fqdn> | Portais | &lt;region>.&lt;fqdn> |
| Portal de administração | adminportal.&lt;region>.&lt;fqdn> | Portais | &lt;region>.&lt;fqdn> |
| Azure Resource Manager Public | management.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| Administrador do Gestor de recursos do Azure | adminmanagement.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Certificado SSL de caráter universal) | Blob Storage | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>(Certificado SSL de caráter universal) | Armazenamento de Tabelas | table.&lt;region>.&lt;fqdn> |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>(Certificado SSL de caráter universal) | Armazenamento de filas | queue.&lt;region>.&lt;fqdn> |
| KeyVault | *.vault.&lt;region>.&lt;fqdn><br>(Certificado SSL de caráter universal) | Cofre de Chaves | vault.&lt;region>.&lt;fqdn> |
| KeyVaultInternal | *.adminvault.&lt;region>.&lt;fqdn><br>(Certificado SSL de caráter universal) |  Cofre de chaves interno |  adminvault.&lt;region>.&lt;fqdn> |
| Anfitrião de extensão de administração | *.adminhosting. \<região >. \<fqdn > (certificados de SSL de caráter universal) | Anfitrião de extensão de administração | adminhosting.\<region>.\<fqdn> |
| Anfitrião de pública de extensão | * .hosting. \<região >. \<fqdn > (certificados de SSL de caráter universal) | Anfitrião de pública de extensão | hosting.\<region>.\<fqdn> |

Se implementar o Azure Stack, utilizando o modo de implementação do Azure AD, só precisa de pedir certificados listados na tabela anterior. No entanto, se implementar o Azure Stack, utilizando o modo de implementação do AD FS, tem também de solicitar certificados descritos na tabela a seguir:

|Pasta de implementação|Assunto do certificado necessário e os nomes alternativos do requerente (SAN)|Âmbito (por região)|Espaço de nomes do subdomínio|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>(SSL Certificate)|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph.*&lt;region>.&lt;fqdn>*<br>(SSL Certificate)|Graph|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Todos os certificados indicados nesta secção tem de ter a mesma palavra-passe. 

## <a name="optional-paas-certificates"></a>Certificados de PaaS opcionais
Se estiver a planear implementar os serviços de PaaS do Azure Stack adicionais (SQL, MySQL e o serviço de aplicações) após o Azure Stack foi implementado e configurado, terá de pedir certificados adicionais para cobrir os pontos de extremidade dos serviços PaaS. 

> [!IMPORTANT]
> Os certificados que utiliza para fornecedores de recursos do serviço de aplicações, o SQL e o MySQL tem de ter a mesma autoridade de raiz como são utilizados para os pontos de extremidade global do Azure Stack. 

A tabela seguinte descreve os pontos finais e os certificados necessários para os adaptadores de SQL e o MySQL e para o serviço de aplicações. Não tem de copiar estes certificados para a pasta de implementação do Azure Stack. Em vez disso, fornecer estes certificados ao instalar os fornecedores de recursos adicionais. 

|Âmbito (por região)|Certificado|Assunto do certificado necessário e os nomes alternativos do requerente (SANs)|Espaço de nomes do subdomínio|
|-----|-----|-----|-----|
|SQL, MySQL|SQL e o MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Certificado SSL de caráter universal)|dbadapter.*&lt;region>.&lt;fqdn>*|
|Serviço de Aplicações|Certificado SSL do Web tráfego predefinido|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Certificado de SSL de caráter universal várias domínio<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Serviço de Aplicações|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL Certificate<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Serviço de Aplicações|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL Certificate<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|Serviço de Aplicações|SSO|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL Certificate<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> requer um certificado com vários nomes alternativos do requerente com carateres universais. Caráter universal várias SANs num único certificado poderá não ser suportado por todas as autoridades de certificação pública 

<sup>2</sup> A &#42;.appservice. *&lt;região >. &lt;fqdn >* certificado de caráter universal não pode ser utilizado em vez destes três certificados (api.appservice. *&lt;região >. &lt;fqdn >*, ftp.appservice. *&lt;região >. &lt;fqdn >* e sso.appservice. *&lt;região >. &lt;fqdn >*. Serviço de aplicações requer explicitamente a utilização de certificados separados para estes pontos finais. 

## <a name="learn-more"></a>Saiba mais
Saiba como [gerar certificados PKI para implementação do Azure Stack](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Passos Seguintes
[Integração de identidade](azure-stack-integrate-identity.md)

