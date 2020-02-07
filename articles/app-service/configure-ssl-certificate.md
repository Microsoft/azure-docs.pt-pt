---
title: Adicionar e gerenciar certificados SSL
description: Crie um certificado gratuito, importe um certificado do serviço de aplicativo, importe um certificado Key Vault ou compre um certificado do serviço de aplicativo no serviço Azure App.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 5df8ae89c16a453b008afed9ee9f8881a0ac4750
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046422"
---
# <a name="add-an-ssl-certificate-in-azure-app-service"></a>Adicionar um certificado SSL no serviço Azure App

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este artigo mostra como criar, carregar ou importar um certificado privado ou um certificado público para o serviço de aplicativo. 

Assim que o certificado for adicionado à sua aplicação ou [aplicação de função](https://docs.microsoft.com/azure/azure-functions/)do App Service, pode [garantir um nome DNS personalizado com ele](configure-ssl-bindings.md) ou [usá-lo no seu código](configure-ssl-certificate-in-code.md)de aplicação .

A tabela a seguir lista as opções que você tem para adicionar certificados no serviço de aplicativo:

|Opção|Descrição|
|-|-|
| Criar um certificado gerenciado do serviço de aplicativo gratuito (versão prévia) | Um certificado privado que é fácil de usar se apenas precisar de garantir o seu [domínio personalizado](app-service-web-tutorial-custom-domain.md) `www` ou qualquer domínio não nu no Serviço de Aplicações. |
| Comprar um certificado do serviço de aplicativo | Um certificado privado que é gerenciado pelo Azure. Ele combina a simplicidade do gerenciamento automatizado de certificados e a flexibilidade das opções de renovação e exportação. |
| Importar um certificado de Key Vault | Útil se utilizar o [Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/) para gerir os seus [certificados PKCS12](https://wikipedia.org/wiki/PKCS_12). Ver [requisitos de certificado privado](#private-certificate-requirements). |
| Carregar um certificado privado | Se você já tiver um certificado privado de um provedor de terceiros, você poderá carregá-lo. Ver [requisitos de certificado privado](#private-certificate-requirements). |
| Carregar um certificado público | Os certificados públicos não são usados para proteger domínios personalizados, mas você pode carregá-los em seu código se precisar que eles acessem recursos remotos. |

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções:

- [Criar uma aplicação de Serviço de Aplicações.](/azure/app-service/)
- Certificado gratuito apenas: mapear um subdomínio (por exemplo, `www.contoso.com`) para o Serviço de Aplicações com um [registo CNAME](app-service-web-tutorial-custom-domain.md#map-a-cname-record).

## <a name="private-certificate-requirements"></a>Requisitos de certificado privado

> [!NOTE]
> As Aplicações Web Azure **não** suportam o AES256 e todos os ficheiros pfx devem ser encriptados com TrippleDES.

O Certificado Gerido pelo Serviço de [Aplicações gratuito](#create-a-free-certificate-preview) ou o certificado de Serviço de [Aplicações](#import-an-app-service-certificate) já satisfazem os requisitos do Serviço de Aplicações. Se você optar por carregar ou importar um certificado privado para o serviço de aplicativo, o certificado deverá atender aos seguintes requisitos:

* Exportado como um ficheiro PFX protegido por [palavra-passe](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)
* Conter uma chave privada com, pelo menos, 2048 bits de comprimento
* Conter todos os certificados intermédios na cadeia de certificados

Para proteger um domínio personalizado em uma associação SSL, o certificado tem requisitos adicionais:

* Contém uma [utilização de chave estendida](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) para autenticação do servidor (OID = 1.3.6.1.5.5.7.3.1)
* Ser assinado por uma autoridade de certificação fidedigna

> [!NOTE]
> Os **certificados de criptografia de curva elíptica (ECC)** podem funcionar com o Serviço de Aplicações, mas não são abordados neste artigo. Trabalhe com a sua autoridade de certificados para saber quais são os passos exatos para criar certificados ECC.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Criar um certificado gratuito (versão prévia)

O certificado gerenciado do serviço de aplicativo gratuito é uma solução de chave ativa para proteger seu nome DNS personalizado no serviço de aplicativo. É um certificado SSL totalmente funcional que é gerenciado pelo serviço de aplicativo e renovado automaticamente. O certificado gratuito vem com as seguintes limitações:

- Não oferece suporte a certificados curinga.
- Não dá suporte a domínios Naked.
- Não é exportável.
- Não suporta registos DNS A.

> [!NOTE]
> O certificado gratuito é emitido por DigiCert. Para alguns domínios de alto nível, deve permitir explicitamente a DigiCert como emitente de certificado, criando um registo de [domínio CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) com o valor: `0 issue digicert.com`.
> 

Para criar um certificado gerenciado do serviço de aplicativo gratuito:

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **App Services** >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, selecione **definições TLS/SSL** > **Certificados de Chave Privadas (.pfx)**  > Criar Certificado Gerido pelo Serviço de **Aplicações**.

![Criar certificado gratuito no serviço de aplicativo](./media/configure-ssl-certificate/create-free-cert.png)

Qualquer domínio não-descoberto que esteja adequadamente mapeado para seu aplicativo com um registro CNAME será listado na caixa de diálogo. Selecione o domínio personalizado para criar um certificado gratuito e selecione **Criar**. Você pode criar apenas um certificado para cada domínio personalizado com suporte.

Quando a operação estiver concluída, consulte o certificado na lista de **Certificados de Chave Privada.**

![Criação de certificado gratuito concluída](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Para proteger um domínio personalizado com esse certificado, você ainda precisa criar uma associação de certificado. Siga os passos em [Criar encadernação.](configure-ssl-bindings.md#create-binding)
>

## <a name="import-an-app-service-certificate"></a>Importar um Certificado do Serviço de Aplicativo

Se você comprar uma Certificado do Serviço de Aplicativo do Azure, o Azure gerenciará as seguintes tarefas:

- Cuida do processo de compra do GoDaddy.
- Executa a verificação de domínio do certificado.
- Mantém o certificado no [Cofre de Chaves Azure.](../key-vault/key-vault-overview.md)
- Gere a renovação do certificado (ver [Certificado Renovar).](#renew-certificate)
- Sincronize o certificado automaticamente com as cópias importadas nos aplicativos do serviço de aplicativo.

Para comprar um certificado de Serviço de Aplicações, vá iniciar a encomenda de [certificado](#start-certificate-order).

Se você já tiver um certificado de serviço de aplicativo em funcionamento, poderá:

- Importar o certificado para o Serviço de [Aplicações.](#import-certificate-into-app-service)
- [Gerir o certificado](#manage-app-service-certificates), como renovar, rekey e exportá-lo.

### <a name="start-certificate-order"></a>Iniciar a ordem do certificado

Inicie uma encomenda de certificado de serviço de aplicações no Certificado de Serviço de <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">Aplicações criar página</a>.

![Iniciar compra de certificado do serviço de aplicativo](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Use a tabela a seguir para ajudá-lo a configurar o certificado. Quando terminar, clique em **Criar**.

| Definição | Descrição |
|-|-|
| Nome | Um nome amigável para seu certificado de serviço de aplicativo. |
| Nome do host do domínio descoberto | Especifique o domínio raiz aqui. O certificado emitido assegura tanto o domínio radicular *como* o subdomínio `www`. No certificado emitido, o campo Nome Comum contém o domínio raiz, e o campo Nome Alternativo sujeito contém o domínio `www`. Para fixar apenas qualquer subdomínio, especifique aqui o nome de domínio totalmente qualificado do subdomínio (por exemplo, `mysubdomain.contoso.com`).|
| Subscrição | A assinatura que conterá o certificado. |
| Grupo de recursos | O grupo de recursos que conterá o certificado. Você pode usar um novo grupo de recursos ou selecionar o mesmo grupo de recursos que o aplicativo do serviço de aplicativo, por exemplo. |
| SKU do certificado | Determina o tipo de certificado a criar, seja um certificado padrão ou um [certificado wildcard.](https://wikipedia.org/wiki/Wildcard_certificate) |
| Termos legais | Clique para confirmar que você concorda com os termos legais. Os certificados são obtidos de GoDaddy. |

### <a name="store-in-azure-key-vault"></a>Armazenar no Azure Key Vault

Após a conclusão do processo de compra do certificado, há mais algumas etapas que você precisa concluir para poder começar a usar esse certificado. 

Selecione o certificado na página de Certificados de Serviço de [Aplicações](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e, em seguida, clique na **configuração** do certificado > **Passo 1: Armazenar**.

![Configurar o armazenamento de Key Vault do certificado do serviço de aplicativo](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) é um serviço Azure que ajuda a salvaguardar chaves criptográficas e segredos usados por aplicações e serviços em nuvem. É o armazenamento de sua escolha para certificados do serviço de aplicativo.

Na página **key vault status,** clique no **Repositório key vault** para criar um novo cofre ou escolha um cofre existente. Se você optar por criar um novo cofre, use a tabela a seguir para ajudá-lo a configurar o cofre e clique em criar. Crie o novo Key Vault dentro da mesma assinatura e grupo de recursos que o aplicativo do serviço de aplicativo.

| Definição | Descrição |
|-|-|
| Nome | Um nome exclusivo que consiste em caracteres alfanuméricos e traços. |
| Grupo de recursos | Como recomendação, selecione o mesmo grupo de recursos que o seu certificado de serviço de aplicativo. |
| Localização | Selecione o mesmo local que o aplicativo do serviço de aplicativo. |
| Escalão de preço | Para obter informações, consulte os detalhes dos [preços do Cofre chave Azure](https://azure.microsoft.com/pricing/details/key-vault/). |
| Políticas de acesso| Define os aplicativos e o acesso permitido aos recursos do cofre. Pode configurá-lo mais tarde, seguindo os passos da [Grant várias aplicações acesso a um cofre chave](../key-vault/key-vault-group-permissions-for-apps.md). |
| Acesso à Rede Virtual | Restringir o acesso ao cofre para determinadas redes virtuais do Azure. Pode configurá-lo mais tarde, seguindo os passos em [Configurar Firewalls e Redes Virtuais](../key-vault/key-vault-network-security.md) |

Assim que escolhero cofre, feche a página de **Repositório do Cofre chave.** O **passo 1: A** opção loja deve mostrar uma marca de verificação verde para o sucesso. Mantenha a página aberta para a próxima etapa.

### <a name="verify-domain-ownership"></a>Verificar a propriedade do domínio

A partir da mesma página de **Configuração** de Certificado que usou no último passo, clique no **passo 2: Verificar**.

![Verificar o domínio do certificado do serviço de aplicativo](./media/configure-ssl-certificate/verify-domain.png)

Selecione Verificação de **Serviço de Aplicações**. Uma vez que já mapeou o domínio para a sua aplicação web (ver [Pré-requisitos),](#prerequisites)já está verificado. Basta clicar **em Verificar** para terminar este passo. Clique no botão **'Actualizar'** até aparecer o Certificado de mensagem Verificado por **domínio.**

> [!NOTE]
> Há suporte para quatro tipos de métodos de verificação de domínio: 
> 
> - Serviço de **Aplicações** - A opção mais conveniente quando o domínio já está mapeado para uma aplicação do App Service na mesma subscrição. Ele aproveita o fato de que o aplicativo do serviço de aplicativo já verificou a propriedade do domínio.
> - **Domínio** - Verifique um domínio do Serviço de [Aplicações que adquiriu ao Azure](manage-custom-dns-buy-domain.md). O Azure adiciona automaticamente o registro TXT de verificação para você e conclui o processo.
> - **Correio** - Verifique o domínio enviando um e-mail ao administrador de domínio. As instruções são fornecidas quando você seleciona a opção.
> - **Manual** - Verifique o domínio utilizando uma página HTML (apenas um certificado**Padrão)** ou um registo DNS TXT. As instruções são fornecidas quando você seleciona a opção.

### <a name="import-certificate-into-app-service"></a>Importar certificado para o serviço de aplicativo

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **App Services** >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, selecione **definições TLS/SSL** > **Certificados de Chave Privadas (.pfx)**  > Certificado de Serviço de **Aplicações de Importação**.

![Importar certificado do serviço de aplicativo no serviço de aplicativo](./media/configure-ssl-certificate/import-app-service-cert.png)

Selecione o certificado que acabou de comprar e selecione **OK**.

Quando a operação estiver concluída, consulte o certificado na lista de **Certificados de Chave Privada.**

![Importação de certificado do serviço de aplicativo concluída](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Para proteger um domínio personalizado com esse certificado, você ainda precisa criar uma associação de certificado. Siga os passos em [Criar encadernação.](configure-ssl-bindings.md#create-binding)
>

## <a name="import-a-certificate-from-key-vault"></a>Importar um certificado de Key Vault

Se utilizar o Cofre de Chaves Azure para gerir os seus certificados, pode importar um certificado PKCS12 do Key Vault para o Serviço de Aplicações, desde que [satisfaça os requisitos](#private-certificate-requirements).

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **App Services** >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, selecione **as definições TLS/SSL** > **Certificados de Chave Privadas (.pfx)**  > Certificado de Cofre de **Chave de Importação**.

![Importar Key Vault certificado no serviço de aplicativo](./media/configure-ssl-certificate/import-key-vault-cert.png)

Use a tabela a seguir para ajudá-lo a selecionar o certificado.

| Definição | Descrição |
|-|-|
| Subscrição | A assinatura à qual o Key Vault pertence. |
| Cofre de Chaves | O cofre com o certificado que você deseja importar. |
| Certificado | Selecione na lista de certificados do PKCS12 no cofre. Todos os certificados de PKCS12 no cofre são listados com suas impressões digitais, mas nem todos têm suporte no serviço de aplicativo. |

Quando a operação estiver concluída, consulte o certificado na lista de **Certificados de Chave Privada.** Se a importação falhar com um erro, o certificado não satisfaz os [requisitos para](#private-certificate-requirements)o Serviço de Aplicações .

![Importação de Key Vault certificado concluída](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Para proteger um domínio personalizado com esse certificado, você ainda precisa criar uma associação de certificado. Siga os passos em [Criar encadernação.](configure-ssl-bindings.md#create-binding)
>

## <a name="upload-a-private-certificate"></a>Carregar um certificado privado

Depois de obter um certificado do seu provedor de certificado, siga as etapas nesta seção para deixá-lo pronto para o serviço de aplicativo.

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

Exporte o certificado SSL intercalado com a chave privada com que o pedido de certificado foi gerado.

Se tiver gerado o pedido de certificado com OpenSSL, significa que criou um ficheiro de chave privada. Para exportar o certificado para PFX, execute o seguinte comando. Substitua os marcadores de posição _&lt;private-key-file>_ e _&lt;merged-certificate-file>_ pelos caminhos para a chave privada e o ficheiro de certificado intercalado.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Quando lhe for pedido, defina uma palavra-passe de exportação. Vai utilizar esta palavra-passe ao carregar o certificado SSL para o Serviço de Aplicações mais tarde.

Se tiver utilizado o IIS ou _Certreq.exe_ para gerar o pedido de certificado, instale o certificado no seu computador local e [exporte-o para PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-certificate-to-app-service"></a>Carregar certificado no serviço de aplicativo

Agora você está pronto para carregar o certificado no serviço de aplicativo.

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **App Services** >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, selecione **definições TLS/SSL** > **Certificados de Chave Privadas (.pfx)**  > **Upload Certificate**.

![Carregar certificado privado no serviço de aplicativo](./media/configure-ssl-certificate/upload-private-cert.png)

Em **Ficheiro de Certificado PFX**, selecione o ficheiro PFX. Em **Palavra-passe do certificado**, escreva a palavra-passe que criou quando exportou o ficheiro PFX. Quando terminar, clique em **Carregar**. 

Quando a operação estiver concluída, consulte o certificado na lista de **Certificados de Chave Privada.**

![Carregamento do certificado concluído](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Para proteger um domínio personalizado com esse certificado, você ainda precisa criar uma associação de certificado. Siga os passos em [Criar encadernação.](configure-ssl-bindings.md#create-binding)
>

## <a name="upload-a-public-certificate"></a>Carregar um certificado público

Os certificados públicos são suportados no formato *.cer.* 

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **App Services** >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, clique nas **definições tLS/SSL** > **Certificados Públicos (.cer)**  > **Carregar Certificado**de Chave Pública .

Em **Nome,** digite um nome para o certificado. No **ficheiro Cer Certificate,** selecione o ficheiro CER.

Clique em **Carregar**.

![Carregar certificado público no serviço de aplicativo](./media/configure-ssl-certificate/upload-public-cert.png)

Uma vez carregado o certificado, copie a impressão digital do certificado e consulte [Tornar o certificado acessível](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>Gerenciar certificados do serviço de aplicativo

Esta secção mostra-lhe como gerir um certificado de Serviço app que adquiriu na Importação de um certificado de Serviço de [Aplicações](#import-an-app-service-certificate).

- [Certificado de rechave](#rekey-certificate)
- [Renovar certificado](#renew-certificate)
- [Certificado de exportação](#export-certificate)
- [Excluir certificado](#delete-certificate)

### <a name="rekey-certificate"></a>Certificado de rechaveamento

Se você considerar que a chave privada do certificado está comprometida, poderá refazer o seu certificado. Selecione o certificado na página de [Certificados](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) de Serviço de Aplicações e, em seguida, selecione **Rekey e Sync** da navegação esquerda.

Clique em **Rekey** para iniciar o processo. Esse processo pode levar de 1-10 a minutos para ser concluído.

![Rechaveamento de um certificado do serviço de aplicativo](./media/configure-ssl-certificate/rekey-app-service-cert.png)

A recriação de chaves do certificado reverte o certificado com um novo certificado emitido pela autoridade de certificação.

Assim que o funcionamento da chave estiver concluído, clique em **Sync**. A operação de sincronização atualiza automaticamente as encadernações do nome de anfitrião para o certificado no Serviço de Aplicações sem causar qualquer inatividade nas suas apps.

> [!NOTE]
> Se não clicar em **Sync,** o Serviço de Aplicações sincroniza automaticamente o seu certificado dentro de 48 horas.

### <a name="renew-certificate"></a>Renovar certificado

Para ativar a renovação automática do seu certificado a qualquer momento, selecione o certificado na página de [Certificados](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) de Serviço de Aplicações e, em seguida, clique em Definições de **Renovação Automática** na navegação à esquerda. Por padrão, os certificados do serviço de aplicativo têm um período de validade de um ano.

Selecione **On** e clique em **Guardar**. Os certificados podem começar a renovar automaticamente 60 dias antes da expiração se você tiver a renovação automática ativada.

![Renovar certificado do serviço de aplicativo automaticamente](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Para renovar manualmente o certificado, clique em **Renovar Manual**. Você pode solicitar para renovar manualmente o certificado 60 dias antes da expiração.

Assim que a operação de renovação estiver concluída, clique em **Sync**. A operação de sincronização atualiza automaticamente as encadernações do nome de anfitrião para o certificado no Serviço de Aplicações sem causar qualquer inatividade nas suas apps.

> [!NOTE]
> Se não clicar em **Sync,** o Serviço de Aplicações sincroniza automaticamente o seu certificado dentro de 48 horas.

### <a name="export-certificate"></a>Exportar o certificado

Como um Certificado de Serviço de Aplicações é um [segredo key vault,](../key-vault/about-keys-secrets-and-certificates.md#key-vault-secrets)você pode exportar uma cópia PFX do mesmo e usá-lo para outros serviços Azure ou fora de Azure.

Para exportar o Certificado de Serviço de Aplicações como ficheiro PFX, execute os seguintes comandos na [Cloud Shell](https://shell.azure.com). Também pode executá-lo localmente se [instalou o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Substitua os espaços reservados pelos nomes utilizados quando [criou o certificado de Serviço de Aplicações](#start-certificate-order).

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

O ficheiro *appservicecertificate.pfx* descarregado é um ficheiro PKCS12 cru que contém certificados públicos e privados. Em cada prompt, use uma cadeia de caracteres vazia para a senha de importação e a frase secreta PEM.

### <a name="delete-certificate"></a>Excluir certificado 

A exclusão de um certificado do serviço de aplicativo é final e irreversível. Qualquer associação no serviço de aplicativo com esse certificado torna-se inválida. Para evitar a exclusão acidental, o Azure coloca um bloqueio no certificado. Para excluir um certificado do serviço de aplicativo, primeiro você deve remover o bloqueio de exclusão no certificado.

Selecione o certificado na página de Certificados de Serviço de [Aplicações](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e, em seguida, selecione **Locks** na navegação à esquerda.

Encontre o cadeado no seu certificado com o tipo de bloqueio **Delete**. À direita, selecione **Delete**.

![Excluir bloqueio para o certificado do serviço de aplicativo](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Agora você pode excluir o certificado do serviço de aplicativo. A partir da navegação à esquerda, selecione **Visão geral** > **Eliminar**. No diálogo de confirmação, escreva o nome do certificado e selecione **OK**.

## <a name="automate-with-scripts"></a>Automatizar com scripts

### <a name="azure-cli"></a>CLI do Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Mais recursos

* [Proteja um nome DNS personalizado com uma ligação SSL](configure-ssl-bindings.md)
* [Impor HTTPS](configure-ssl-bindings.md#enforce-https)
* [Impor TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Utilize um certificado SSL no seu código de aplicação](configure-ssl-certificate-in-code.md)
* [FAQ : Certificados de serviço de aplicações](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
