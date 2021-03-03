---
title: Adicionar e gerir certificados TLS/SSL
description: Crie um certificado gratuito, importe um certificado de Serviço de Aplicações, importe um certificado Key Vault ou compre um certificado de Serviço de Aplicações no Azure App Service.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: e563981d3a68375105256aa6015aa94ada91326b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711710"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>Adicionar um certificado TLS/SSL no Serviço de Aplicações do Azure

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este artigo mostra-lhe como criar, carregar ou importar um certificado privado ou um certificado público no Serviço de Aplicações. 

Uma vez que o certificado seja adicionado à sua app de Serviço de Aplicações ou [aplicação de função,](../azure-functions/index.yml)pode [garantir um nome DNS personalizado com ele](configure-ssl-bindings.md) ou [usá-lo no seu código de aplicação](configure-ssl-certificate-in-code.md).

> [!NOTE]
> Um certificado enviado para uma aplicação é armazenado numa unidade de implantação que está ligada à combinação de recursos e região da aplicação (internamente chamada de *webspace).* Isto torna o certificado acessível a outras aplicações no mesmo grupo de recursos e combinação de região. 

A tabela que se segue lista as opções que tem para adicionar certificados no Serviço de Aplicações:

|Opção|Descrição|
|-|-|
| Criar um Certificado Gerido de Serviço de Aplicações gratuita (Pré-visualização) | Um certificado privado que é fácil de usar se apenas precisar de proteger o seu `www` [domínio personalizado](app-service-web-tutorial-custom-domain.md) ou qualquer domínio não nu no Serviço de Aplicações. |
| Comprar um certificado de Serviço de Aplicações | Um certificado privado que é gerido pelo Azure. Combina a simplicidade da gestão automatizada de certificados e a flexibilidade das opções de renovação e exportação. |
| Importar um certificado do Cofre-Chave | Útil se utilizar [o Cofre da Chave Azure](../key-vault/index.yml) para gerir os seus [certificados PKCS12](https://wikipedia.org/wiki/PKCS_12). Consulte [os requisitos de certificado privado.](#private-certificate-requirements) |
| Faça upload de um certificado privado | Se já tem um certificado privado de um fornecedor de terceiros, pode carregá-lo. Consulte [os requisitos de certificado privado.](#private-certificate-requirements) |
| Faça upload de um certificado público | Os certificados públicos não são usados para garantir domínios personalizados, mas pode carregá-los no seu código se precisar deles para aceder a recursos remotos. |

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar este guia:

- [Criar uma aplicação de Serviço de Aplicações.](./index.yml)
- Certificado gratuito apenas: mapear um subdomínio (por exemplo, `www.contoso.com` ) para o Serviço de Aplicações com um registo [CNAME](app-service-web-tutorial-custom-domain.md#map-a-cname-record).

## <a name="private-certificate-requirements"></a>Requisitos de certificados privados

> [!NOTE]
> O Azure Web Apps **não** suporta AES256 e todos os ficheiros pfx devem ser encriptados com TripleDES.

O [Certificado Gerido do Serviço de Aplicações gratuito](#create-a-free-certificate-preview) ou o certificado de Serviço de [Aplicações](#import-an-app-service-certificate) já satisfazem os requisitos do Serviço de Aplicações. Se optar por fazer o upload ou importação de um certificado privado para o Serviço de Aplicações, o seu certificado deve satisfazer os seguintes requisitos:

* Exportado como um [ficheiro PFX protegido por palavra-passe](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)
* Conter uma chave privada com, pelo menos, 2048 bits de comprimento
* Conter todos os certificados intermédios na cadeia de certificados

Para garantir um domínio personalizado numa ligação TLS, o certificado tem requisitos adicionais:

* Contém uma [utilização da chave estendida](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) para a autenticação do servidor (OID = 1.3.6.1.5.5.7.3.1)
* Ser assinado por uma autoridade de certificação fidedigna

> [!NOTE]
> Os **certificados de criptografia de curva elíptica (ECC)** podem funcionar com o Serviço de Aplicações, mas não são abordados neste artigo. Trabalhe com a sua autoridade de certificados para saber quais são os passos exatos para criar certificados ECC.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Criar um certificado gratuito (Pré-visualização)

O Certificado Gerido por Serviço de Aplicações gratuito é uma solução chave-na-mão para garantir o nome DNS personalizado no Serviço de Aplicações. É um certificado TLS/SSL totalmente funcional que é gerido pelo App Service e renovado automaticamente. O certificado gratuito vem com as seguintes limitações:

- Não suporta certificados wildcard.
- Não suporta domínios nus.
- Não é exportável.
- Não é suportado no Ambiente de Serviço de Aplicações (ASE)
- Não suporta registos A. Por exemplo, a renovação automática não funciona com registos A.

> [!NOTE]
> O certificado gratuito é emitido pela DigiCert. Para alguns domínios de alto nível, deve explicitamente permitir o DigiCert como emitente de certificado, criando um [registo de domínio CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) com o valor: `0 issue digicert.com` .
> 

Para criar um Certificado Gerido de Serviço de Aplicações gratuito:

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **Serviços de Aplicações**  >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, selecione **TLS/SSL**  >  **configurações Certificados chave privados (.pfx)**  >  **Criar Certificado Gerido de Serviço de Aplicação**.

![Criar certificado gratuito no Serviço de Aplicações](./media/configure-ssl-certificate/create-free-cert.png)

Qualquer domínio não nu que esteja devidamente mapeado para a sua aplicação com um registo CNAME está listado no diálogo. Selecione o domínio personalizado para criar um certificado gratuito e selecione **Criar**. Pode criar apenas um certificado para cada domínio personalizado suportado.

Quando a operação estiver concluída, consulte o certificado na lista **de Certificados-Chave Privados.**

![Criar certificado gratuito terminado](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Para garantir um domínio personalizado com este certificado, ainda precisa de criar um certificado vinculativo. Siga os passos em [Criar ligação](configure-ssl-bindings.md#create-binding).
>

## <a name="import-an-app-service-certificate"></a>Importar um Certificado de Serviço de Aplicações

Se comprar um Certificado de Serviço de Aplicações da Azure, a Azure gere as seguintes tarefas:

- Cuida do processo de compra do GoDaddy.
- Realiza a verificação de domínio do certificado.
- Mantém o certificado no [Cofre da Chave Azure.](../key-vault/general/overview.md)
- Gere a renovação do certificado (ver [Certificado de Renovação).](#renew-certificate)
- Sincronizar o certificado automaticamente com as cópias importadas em aplicações do Serviço de Aplicações.

Para adquirir um certificado de Serviço de Aplicações, vá à [ordem de certificado Iniciar.](#start-certificate-order)

Se já tem um certificado de Serviço de Aplicações em funcionamento, pode:

- [Importe o certificado no Serviço de Aplicações.](#import-certificate-into-app-service)
- [Gerir o certificado](#manage-app-service-certificates), como renovar, rekey e exportá-lo.
> [!NOTE]
> Os Certificados de Serviço de Aplicações não são suportados em Nuvens Nacionais Azure neste momento.

### <a name="start-certificate-order"></a>Iniciar pedido de certificado

Inicie uma ordem de certificado de Serviço de Aplicação na <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">página de criação do Certificado de Serviço de Aplicações.</a>

![Iniciar a compra de certificado de Serviço de Aplicação](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Utilize a seguinte tabela para ajudá-lo a configurar o certificado. Quando terminar, clique em **Criar**.

| Definições | Descrição |
|-|-|
| Nome | Um nome amigável para o seu certificado de Serviço de Aplicações. |
| Nome do anfitrião do domínio nu | Especifique o domínio raiz aqui. O certificado emitido assegura tanto o domínio raiz *como* o `www` subdomínio. No certificado emitido, o campo Nome Comum contém o domínio raiz, e o campo nome alternativo sujeito contém o `www` domínio. Para garantir apenas qualquer subdomínio, especifique o nome de domínio totalmente qualificado do subdomínio aqui (por exemplo, `mysubdomain.contoso.com` ).|
| Subscrição | A assinatura que conterá o certificado. |
| Grupo de recursos | O grupo de recursos que conterá o certificado. Pode utilizar um novo grupo de recursos ou selecionar o mesmo grupo de recursos que a sua aplicação App Service, por exemplo. |
| Certificado SKU | Determina o tipo de certificado para criar, seja um certificado padrão ou um [certificado wildcard](https://wikipedia.org/wiki/Wildcard_certificate). |
| Termos Legais | Clique para confirmar que concorda com os termos legais. Os certificados são obtidos do GoDaddy. |

> [!NOTE]
> Os Certificados de Serviço de Aplicações adquiridos à Azure são emitidos pela GoDaddy. Para alguns domínios de alto nível, deve explicitamente permitir o GoDaddy como emitente de certificado, criando um [registo de domínio CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) com o valor: `0 issue godaddy.com`
> 

### <a name="store-in-azure-key-vault"></a>Loja em Azure Key Vault

Uma vez concluído o processo de compra do certificado, há mais alguns passos que precisa de completar antes de poder começar a usar este certificado. 

Selecione o certificado na página [de Certificados de Serviço](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) de Aplicação e, em seguida, clique em Certificate **Configuration**  >  **Step 1: Store**.

![Configure o armazenamento do cofre chave do certificado de Serviço de Aplicações](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](../key-vault/general/overview.md) é um serviço Azure que ajuda a salvaguardar chaves criptográficas e segredos usados por aplicações e serviços em nuvem. É o armazenamento de eleição para certificados de Serviço de Aplicações.

Na página **Key Vault Status,** clique no **Repositório do Cofre de Chaves** para criar um novo cofre ou escolha um cofre existente. Se optar por criar um novo cofre, utilize a seguinte tabela para o ajudar a configurar o cofre e clique em Criar. Crie o novo Key Vault dentro do mesmo grupo de subscrição e recursos que a sua aplicação App Service.

| Definições | Descrição |
|-|-|
| Nome | Um nome único que consiste em caracteres alfanuméricos e traços. |
| Grupo de recursos | Como recomendação, selecione o mesmo grupo de recursos que o certificado de Serviço de Aplicações. |
| Localização | Selecione o mesmo local que a sua aplicação App Service. |
| Escalão de preço | Para obter informações, consulte [os detalhes dos preços do Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Políticas de acesso| Define as aplicações e o acesso permitido aos recursos do cofre. Pode configurgê-lo mais tarde, seguindo os passos na [política de acesso a Um Cofre de Chaves](../key-vault/general/assign-access-policy-portal.md). |
| Acesso virtual à rede | Restringir o acesso ao cofre a certas redes virtuais Azure. Pode configugá-lo mais tarde, seguindo os passos em [Configure Azure Key Vault Firewalls e Redes Virtuais](../key-vault/general/network-security.md) |

Assim que tiver selecionado o cofre, feche a página do **Repositório** do Cofre de Chaves. A opção **Passo 1: A** opção de loja deve mostrar uma marca de verificação verde para o sucesso. Mantenha a página aberta para o próximo passo.

### <a name="verify-domain-ownership"></a>Verificar a propriedade do domínio

A partir da mesma página **de Configuração** de Certificado que usou no último passo, clique no **Passo 2: Verifique**.

![Verifique o domínio do certificado de Serviço de Aplicações](./media/configure-ssl-certificate/verify-domain.png)

Selecione **a verificação do serviço de aplicações.** Uma vez que já mapeou o domínio para a sua aplicação web (ver [Pré-requisitos),](#prerequisites)já está verificado. Basta clicar **Verifique** para terminar este passo. Clique no botão **'Atualizar'** até aparecer o certificado de mensagem **"Controlo de Domínio".**

> [!NOTE]
> São suportados quatro tipos de métodos de verificação de domínio: 
> 
> - **Serviço de Aplicações** - A opção mais conveniente quando o domínio já está mapeado para uma aplicação do Serviço de Aplicações na mesma subscrição. Aproveita o facto de a app App Service já ter verificado a propriedade do domínio.
> - **Domínio** - Verifique um [domínio de Serviço de Aplicações que adquiriu ao Azure](manage-custom-dns-buy-domain.md). O Azure adiciona automaticamente o registo TXT de verificação para si e completa o processo.
> - **Correio** - Verifique o domínio enviando um e-mail ao administrador de domínio. As instruções são fornecidas quando seleciona a opção.
> - **Manual** - Verifique o domínio utilizando uma página HTML (certificado **padrão** apenas) ou um registo DNS TXT. As instruções são fornecidas quando seleciona a opção.

### <a name="import-certificate-into-app-service"></a>Certificado de importação para o Serviço de Aplicações

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **Serviços de Aplicações**  >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, selecione **TLS/SSL configura**  >  **certificados de chave privada (.pfx)**  >  **Certificado de Serviço de Aplicações de Importação**.

![Certificado de Serviço de Aplicações de Importação no Serviço de Aplicações](./media/configure-ssl-certificate/import-app-service-cert.png)

Selecione o certificado que acabou de adquirir e selecione **OK**.

Quando a operação estiver concluída, consulte o certificado na lista **de Certificados-Chave Privados.**

![Certificado de Serviço de Aplicações de Importação concluído](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Para garantir um domínio personalizado com este certificado, ainda precisa de criar um certificado vinculativo. Siga os passos em [Criar ligação](configure-ssl-bindings.md#create-binding).
>

## <a name="import-a-certificate-from-key-vault"></a>Importar um certificado do Cofre-Chave

Se utilizar o Azure Key Vault para gerir os seus certificados, pode importar um certificado PKCS12 da Key Vault para o Serviço de Aplicações, desde que [satisfaça os requisitos.](#private-certificate-requirements)

### <a name="authorize-app-service-to-read-from-the-vault"></a>Autorizar o Serviço de Aplicações a ler a partir do cofre
Por padrão, o fornecedor de recursos do Serviço de Aplicações não tem acesso ao Cofre de Chaves. Para utilizar um Cofre-Chave para uma colocação de certificado, é necessário [autorizar o fornecedor de recursos a ler o acesso ao KeyVault](../key-vault/general/assign-access-policy-cli.md). 

`abfa0a7c-a6b6-4736-8310-5855508787cd`  é o nome principal do serviço de fornecedor de recursos para o Serviço de Aplicações, e é o mesmo para todas as subscrições do Azure. Para o ambiente em nuvem do Governo Azure, use `6a02c803-dafd-4136-b4c3-5a6f318b4714` como nome principal do serviço de prestador de recursos.

### <a name="import-a-certificate-from-your-vault-to-your-app"></a>Importe um certificado do seu cofre para a sua app

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **Serviços de Aplicações**  >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, selecione **TLS/SSL**  >  **configurações Certificados chave privados (.pfx)**  >  **Certificado de Cofre chave de importação**.

![Certificado de cofre chave de importação no Serviço de Aplicações](./media/configure-ssl-certificate/import-key-vault-cert.png)

Utilize a seguinte tabela para ajudá-lo a selecionar o certificado.

| Definições | Descrição |
|-|-|
| Subscrição | A assinatura a que pertence o Cofre-Chave. |
| Key Vault | O cofre com o certificado que quer importar. |
| Certificado | Selecione a partir da lista de certificados PKCS12 no cofre. Todos os certificados PKCS12 do cofre estão listados com as suas impressões digitais, mas nem todos são suportados no Serviço de Aplicações. |

Quando a operação estiver concluída, consulte o certificado na lista **de Certificados-Chave Privados.** Se a importação falhar com um erro, o certificado não satisfaz os [requisitos do Serviço de Aplicações.](#private-certificate-requirements)

![Certificado de Abóbada de Chave de Importação concluído](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!NOTE]
> Se atualizar o seu certificado no Key Vault com um novo certificado, o Serviço de Aplicações sincroniza automaticamente o seu certificado dentro de 48 horas.

> [!IMPORTANT] 
> Para garantir um domínio personalizado com este certificado, ainda precisa de criar um certificado vinculativo. Siga os passos em [Criar ligação](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-private-certificate"></a>Faça upload de um certificado privado

Assim que obtiver um certificado do seu fornecedor de certificados, siga os passos desta secção para o preparar para o Serviço de Aplicações.

### <a name="merge-intermediate-certificates"></a>Intercalar certificados intermédios

Se a sua autoridade de certificação lhe der vários certificados na cadeia de certificados, terá de intercalá-los por ordem.

Para tal, abra cada certificado recebido num editor de texto.

Crie um ficheiro para o certificado intercalado, denominado _mergedcertificate.crt_. Num editor de texto, copie o conteúdo de cada certificado para este ficheiro. A ordem dos seus certificados deve seguir a ordem na cadeia de certificados, a começar no seu certificado e a terminar no certificado de raiz. O aspeto é igual ao do exemplo abaixo:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportar o certificado para PFX

Exporte o seu certificado TLS/SSL fundido com a chave privada com a qual o seu pedido de certificado foi gerado.

Se tiver gerado o pedido de certificado com OpenSSL, significa que criou um ficheiro de chave privada. Para exportar o certificado para PFX, execute o seguinte comando. Substitua os espaços reservados>de _&lt; ficheiros de chave privada_ e>de ficheiro de certificado _&lt; fundido_ pelos caminhos da sua chave privada e do seu ficheiro de certificado fundido.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Quando lhe for pedido, defina uma palavra-passe de exportação. Utilizará esta palavra-passe ao enviar o seu certificado TLS/SSL para o Serviço de Aplicações mais tarde.

Se tiver utilizado o IIS ou _Certreq.exe_ para gerar o pedido de certificado, instale o certificado no seu computador local e [exporte-o para PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="upload-certificate-to-app-service"></a>Certificado de upload para o Serviço de Aplicações

Está agora pronto para enviar o certificado para o Serviço de Aplicações.

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **Serviços de Aplicações**  >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, selecione **certificados**  >  **de porta-chaves privados (.pfx)** De  >  configuração (.pfx).

![Faça upload de certificado privado no Serviço de Aplicações](./media/configure-ssl-certificate/upload-private-cert.png)

Em **Ficheiro de Certificado PFX**, selecione o ficheiro PFX. Em **Palavra-passe do certificado**, escreva a palavra-passe que criou quando exportou o ficheiro PFX. Quando terminar, clique em **Upload**. 

Quando a operação estiver concluída, consulte o certificado na lista **de Certificados-Chave Privados.**

![Certificado de upload terminado](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Para garantir um domínio personalizado com este certificado, ainda precisa de criar um certificado vinculativo. Siga os passos em [Criar ligação](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-public-certificate"></a>Faça upload de um certificado público

Os certificados públicos são suportados no formato *.cer.* 

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **Serviços de Aplicações**  >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, clique em **Certificados Públicos de Definições TLS/SSL**  >  **(.cer)**  >  **Upload Public Key Certificate**.

Em **Nome,** escreva um nome para o certificado. No **ficheiro Certificado CER,** selecione o seu ficheiro CER.

Clique em **Carregar**.

![Upload de certificado público no Serviço de Aplicações](./media/configure-ssl-certificate/upload-public-cert.png)

Uma vez que o certificado é carregado, copie a impressão digital do certificado e veja [tornar o certificado acessível](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>Gerir certificados de Serviço de Aplicações

Esta secção mostra-lhe como gerir um certificado de Serviço de Aplicações que adquiriu no [Certificado de Serviço de Aplicações.](#import-an-app-service-certificate)

- [Certificado rekey](#rekey-certificate)
- [Renovar certificado](#renew-certificate)
- [Exportar o certificado](#export-certificate)
- [Apagar certificado](#delete-certificate)

### <a name="rekey-certificate"></a>Certificado rekey

Se acha que a chave privada do certificado está comprometida, pode re-conseguir o seu certificado. Selecione o certificado na página ['Certificados de Serviço de Aplicação'](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e, em seguida, selecione **Rekey e Sync** a partir da navegação à esquerda.

Clique **em Rekey** para iniciar o processo. Este processo pode demorar 1 a 10 minutos a ser concluído.

![Rekey um certificado de Serviço de Aplicações](./media/configure-ssl-certificate/rekey-app-service-cert.png)

A rekeying do seu certificado revira o certificado com um novo certificado emitido pela autoridade de certificados.

Assim que a operação de rekey estiver concluída, clique em **Sync**. A operação de sincronização atualiza automaticamente as ligações do nome anfitrião para o certificado no Serviço de Aplicações sem causar qualquer tempo de inatividade nas suas apps.

> [!NOTE]
> Se não clicar em **Sync,** o Serviço de Aplicações sincroniza automaticamente o seu certificado dentro de 48 horas.

### <a name="renew-certificate"></a>Renovar certificado

Para ligar a renovação automática do seu certificado a qualquer momento, selecione o certificado na página ['Certificados de Serviço de Aplicação'](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e, em seguida, clique em **Definições de Renovação Automática** na navegação à esquerda. Por predefinição, os Certificados de Serviço de Aplicações têm um período de validade de um ano.

Selecione **On** e clique **Em Guardar**. Os certificados podem começar a renovar automaticamente 60 dias antes do termo se tiver a renovação automática ligada.

![Renovar automaticamente o certificado do Serviço de Aplicações](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Para renovar manualmente o certificado, clique em **Renovar Manualmente.** Pode solicitar a renovação manual do seu certificado 60 dias antes do termo.

Assim que a operação de renovação estiver concluída, clique em **Sync**. A operação de sincronização atualiza automaticamente as ligações do nome anfitrião para o certificado no Serviço de Aplicações sem causar qualquer tempo de inatividade nas suas apps.

> [!NOTE]
> Se não clicar em **Sync,** o Serviço de Aplicações sincroniza automaticamente o seu certificado dentro de 48 horas.

### <a name="export-certificate"></a>Exportar o certificado

Como um Certificado de Serviço de Aplicações é um [segredo key vault,](../key-vault/general/about-keys-secrets-certificates.md)você pode exportar uma cópia PFX do mesmo e usá-lo para outros serviços Azure ou fora de Azure.

Para exportar o Certificado de Serviço de Aplicações como um ficheiro PFX, execute os seguintes comandos na [Cloud Shell](https://shell.azure.com). Também pode executá-lo localmente se [instalar o Azure CLI](/cli/azure/install-azure-cli). Substitua os espaços reservados pelos nomes utilizados quando [criou o certificado de Serviço de Aplicações.](#start-certificate-order)

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

O ficheiro *appservicecertificate.pfx* descarregado é um ficheiro PKCS12 cru que contém certificados públicos e privados. Em cada pedido, utilize uma corda vazia para a senha de importação e a frase de passe PEM.

### <a name="delete-certificate"></a>Apagar certificado 

A eliminação de um certificado de Serviço de Aplicações é definitiva e irreversível. A supressão de um recurso de Certificado de Serviço de Aplicações resulta na revogação do certificado. Qualquer encadernação no Serviço de Aplicações com este certificado torna-se inválida. Para evitar a eliminação acidental, a Azure coloca um cadeado no certificado. Para eliminar um certificado de Serviço de Aplicações, deve primeiro remover o bloqueio de eliminação no certificado.

Selecione o certificado na página ['Certificados de Serviço de Aplicação'](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e, em seguida, selecione **Fechaduras** na navegação à esquerda.

Encontre o bloqueio no seu certificado com o tipo de bloqueio **Delete**. À sua direita, selecione **Delete**.

![Excluir bloqueio para certificado de Serviço de Aplicações](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Agora pode apagar o certificado de Serviço de Aplicações. A partir da navegação à esquerda, selecione **Overview**  >  **Delete**. No diálogo de confirmação, digite o nome do certificado e selecione **OK**.

## <a name="automate-with-scripts"></a>Automatizar com scripts

### <a name="azure-cli"></a>CLI do Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Mais recursos

* [Proteja um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service](configure-ssl-bindings.md)
* [Impor HTTPS](configure-ssl-bindings.md#enforce-https)
* [Impor TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Utilizar um certificado TLS/SSL no código no Serviço de Aplicações do Azure](configure-ssl-certificate-in-code.md)
* [FAQ : Certificados de Serviço de Aplicações](./faq-configuration-and-management.md)
