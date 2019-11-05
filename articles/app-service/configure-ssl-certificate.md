---
title: Adicionar e gerenciar certificados SSL-serviço de Azure App | Microsoft Docs
description: Saiba como comprar um certificado do serviço de aplicativo e associá-lo ao seu aplicativo do serviço de aplicativo
services: app-service
author: cephalin
manager: gwallace
tags: buy-ssl-certificates
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 683ea55bdc9eb878f7bcbe832d5364d50a6b23e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501184"
---
# <a name="add-an-ssl-certificate-in-azure-app-service"></a>Adicionar um certificado SSL no serviço Azure App

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este artigo mostra como criar, carregar ou importar um certificado privado ou um certificado público para o serviço de aplicativo. 

Depois que o certificado for adicionado ao aplicativo do serviço de aplicativo ou ao [aplicativo de funções](https://docs.microsoft.com/azure/azure-functions/), você poderá [proteger um nome DNS personalizado com ele](configure-ssl-bindings.md) ou [usá-lo no código do aplicativo](configure-ssl-certificate-in-code.md).

A tabela a seguir lista as opções que você tem para adicionar certificados no serviço de aplicativo:

|Opção|Descrição|
|-|-|
| Criar um certificado gerenciado do serviço de aplicativo gratuito (versão prévia) | Um certificado privado que é fácil de usar se você só precisa proteger seu `www` [domínio personalizado](app-service-web-tutorial-custom-domain.md) ou qualquer domínio não descoberto no serviço de aplicativo. |
| Comprar um certificado do serviço de aplicativo | Um certificado privado que é gerenciado pelo Azure. Ele combina a simplicidade do gerenciamento automatizado de certificados e a flexibilidade das opções de renovação e exportação. |
| Importar um certificado de Key Vault | Útil se você usar [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) para gerenciar seus [certificados PKCS12](https://wikipedia.org/wiki/PKCS_12). Consulte [requisitos de certificado privado](#private-certificate-requirements). |
| Carregar um certificado privado | Se você já tiver um certificado privado de um provedor de terceiros, você poderá carregá-lo. Consulte [requisitos de certificado privado](#private-certificate-requirements). |
| Carregar um certificado público | Os certificados públicos não são usados para proteger domínios personalizados, mas você pode carregá-los em seu código se precisar que eles acessem recursos remotos. |

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções:

- [Crie um aplicativo do serviço de aplicativo](/azure/app-service/).
- Somente certificado gratuito: mapeie um subdomínio (por exemplo, `www.contoso.com`) para o serviço de aplicativo com um [registro CNAME](app-service-web-tutorial-custom-domain.md#map-a-cname-record).

## <a name="private-certificate-requirements"></a>Requisitos de certificado privado

O [certificado gerenciado do serviço de aplicativo gratuito](#create-a-free-certificate-preview) ou o [certificado do serviço de aplicativo](#import-an-app-service-certificate) já atende aos requisitos do serviço de aplicativo. Se você optar por carregar ou importar um certificado privado para o serviço de aplicativo, o certificado deverá atender aos seguintes requisitos:

* Exportado como um [arquivo PFX protegido por senha](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)
* Conter uma chave privada com, pelo menos, 2048 bits de comprimento
* Conter todos os certificados intermédios na cadeia de certificados

Para proteger um domínio personalizado em uma associação SSL, o certificado tem requisitos adicionais:

* Contém um [uso estendido de chave](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) para autenticação do servidor (OID = 1.3.6.1.5.5.7.3.1)
* Ser assinado por uma autoridade de certificação fidedigna

> [!NOTE]
> Os **certificados de criptografia de curva elíptica (ECC)** podem funcionar com o Serviço de Aplicações, mas não são abordados neste artigo. Trabalhe com a sua autoridade de certificados para saber quais são os passos exatos para criar certificados ECC.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Criar um certificado gratuito (versão prévia)

O certificado gerenciado do serviço de aplicativo gratuito é uma solução de chave ativa para proteger seu nome DNS personalizado no serviço de aplicativo. É um certificado SSL totalmente funcional que é gerenciado pelo serviço de aplicativo e renovado automaticamente. O certificado gratuito vem com as seguintes limitações:

- Não dá suporte a domínios Naked.
- Não é exportável.

Para criar um certificado gerenciado do serviço de aplicativo gratuito:

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, no menu à esquerda, selecione **serviços de aplicativos** >  **\<nome-do-aplicativo >** .

No painel de navegação à esquerda do seu aplicativo, selecione **configurações de TLS/SSL** > **certificados de chave privada (. pfx)**  > **criar certificado gerenciado do serviço de aplicativo**.

![Criar certificado gratuito no serviço de aplicativo](./media/configure-ssl-certificate/create-free-cert.png)

Qualquer domínio não-descoberto que esteja adequadamente mapeado para seu aplicativo com um registro CNAME será listado na caixa de diálogo. Selecione o domínio personalizado para criar um certificado gratuito e selecione **criar**. Você pode criar apenas um certificado para cada domínio personalizado com suporte.

Quando a operação for concluída, você verá o certificado na lista **certificados de chave privada** .

![Criação de certificado gratuito concluída](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Para proteger um domínio personalizado com esse certificado, você ainda precisa criar uma associação de certificado. Siga as etapas em [criar Associação](configure-ssl-bindings.md#create-binding).
>

## <a name="import-an-app-service-certificate"></a>Importar um Certificado do Serviço de Aplicativo

Se você comprar uma Certificado do Serviço de Aplicativo do Azure, o Azure gerenciará as seguintes tarefas:

- Cuida do processo de compra do GoDaddy.
- Executa a verificação de domínio do certificado.
- Mantém o certificado no [Azure Key Vault](../key-vault/key-vault-overview.md).
- Gerencia a renovação de certificado (consulte [renovar certificado](#renew-certificate)).
- Sincronize o certificado automaticamente com as cópias importadas nos aplicativos do serviço de aplicativo.

Para comprar um certificado do serviço de aplicativo, acesse [iniciar a ordem do certificado](#start-certificate-order).

Se você já tiver um certificado de serviço de aplicativo em funcionamento, poderá:

- [Importe o certificado para o serviço de aplicativo](#import-certificate-into-app-service).
- [Gerencie o certificado](#manage-app-service-certificates), como renovar, refazer o chaveamento e exportá-lo.

### <a name="start-certificate-order"></a>Iniciar a ordem do certificado

Inicie uma ordem de certificado do serviço de aplicativo na <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">página certificado do serviço de aplicativo criar</a>.

![Iniciar compra de certificado do serviço de aplicativo](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Use a tabela a seguir para ajudá-lo a configurar o certificado. Quando terminar, clique em **Criar**.

| Definição | Descrição |
|-|-|
| Nome | Um nome amigável para seu certificado de serviço de aplicativo. |
| Nome do host do domínio descoberto | Especifique o domínio raiz aqui. *O certificado emitido protege o domínio* raiz e o subdomínio `www`. No certificado emitido, o campo nome comum contém o domínio raiz e o campo nome alternativo da entidade contém o domínio `www`. Para proteger somente qualquer subdomínio, especifique o nome de domínio totalmente qualificado do subdomínio aqui (por exemplo, `mysubdomain.contoso.com`).|
| Subscrição | O centro de dados onde o a aplicação Web está alojada. |
| Grupo de recursos | O grupo de recursos que contém o certificado. Você pode usar um novo grupo de recursos ou selecionar o mesmo grupo de recursos que o aplicativo do serviço de aplicativo, por exemplo. |
| SKU do certificado | Determina o tipo de certificado a ser criado, se um certificado padrão ou um [certificado curinga](https://wikipedia.org/wiki/Wildcard_certificate). |
| Termos legais | Clique para confirmar que você concorda com os termos legais. Os certificados são obtidos de GoDaddy. |

### <a name="store-in-azure-key-vault"></a>Armazenar no Azure Key Vault

Após a conclusão do processo de compra do certificado, há mais algumas etapas que você precisa concluir para poder começar a usar esse certificado. 

Selecione o certificado na página [certificados do serviço de aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e clique em **configuração do certificado** > **etapa 1: armazenar**.

![Configurar o armazenamento de Key Vault do certificado do serviço de aplicativo](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) é um serviço do Azure que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. É o armazenamento de sua escolha para certificados do serviço de aplicativo.

Na página **status da Key Vault** , clique em **repositório Key Vault** para criar um novo cofre ou escolha um cofre existente. Se você optar por criar um novo cofre, use a tabela a seguir para ajudá-lo a configurar o cofre e clique em criar. consulte para criar novos Key Vault dentro da mesma assinatura e grupo de recursos.

| Definição | Descrição |
|-|-|
| Nome | Um nome exclusivo que consiste em caracteres alfanuméricos e traços. |
| Grupo de recursos | Como recomendação, selecione o mesmo grupo de recursos que o seu certificado de serviço de aplicativo. |
| Localização | Selecione o mesmo local que o aplicativo do serviço de aplicativo. |
| Escalão de preço | Para obter informações, consulte [Azure Key Vault detalhes de preços](https://azure.microsoft.com/pricing/details/key-vault/). |
| Políticas de acesso| Define os aplicativos e o acesso permitido aos recursos do cofre. Você pode configurá-lo mais tarde, seguindo as etapas em [conceder a vários aplicativos acesso a um cofre de chaves](../key-vault/key-vault-group-permissions-for-apps.md). |
| Acesso à rede virtual | Restringir o acesso ao cofre para determinadas redes virtuais do Azure. Você pode configurá-lo mais tarde, seguindo as etapas em [configurar Azure Key Vault firewalls e redes virtuais](../key-vault/key-vault-network-security.md) |

Depois de selecionar o cofre, feche a página do **repositório Key Vault** . A opção de **repositório** deve mostrar uma marca de seleção verde para êxito. Mantenha a página aberta para a próxima etapa.

### <a name="verify-domain-ownership"></a>Verificar a propriedade do domínio

Na mesma página de **configuração de certificado** usada na última etapa, clique em **etapa 2: verificar**.

![Verificar o domínio do certificado do serviço de aplicativo](./media/configure-ssl-certificate/verify-domain.png)

Selecione **verificação do serviço de aplicativo**. Como você já mapeou o domínio para seu aplicativo Web (consulte [pré-requisitos](#prerequisites)), ele já está verificado. Basta clicar em **verificar** para concluir esta etapa. Clique no botão **Atualizar** até que o **certificado de mensagem seja verificado pelo domínio** .

> [!NOTE]
> Há suporte para quatro tipos de métodos de verificação de domínio: 
> 
> - **Serviço de aplicativo** -a opção mais conveniente quando o domínio já está mapeado para um aplicativo do serviço de aplicativo na mesma assinatura. Ele aproveita o fato de que o aplicativo do serviço de aplicativo já verificou a propriedade do domínio.
> - **Domínio** -verifique um [domínio do serviço de aplicativo que você comprou do Azure](manage-custom-dns-buy-domain.md). O Azure adiciona automaticamente o registro TXT de verificação para você e conclui o processo.
> - **Email** -Verifique o domínio enviando um email para o administrador de domínio. As instruções são fornecidas quando você seleciona a opção.
> - **Manual** -Verifique o domínio usando uma página HTML (somente certificado**padrão** ) ou um registro txt do DNS. As instruções são fornecidas quando você seleciona a opção.

### <a name="import-certificate-into-app-service"></a>Importar certificado para o serviço de aplicativo

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, no menu à esquerda, selecione **serviços de aplicativos** >  **\<nome-do-aplicativo >** .

No painel de navegação à esquerda do seu aplicativo, selecione **configurações de TLS/SSL** > **certificados de chave privada (. pfx)**  > **importar certificado do serviço de aplicativo**.

![Importar certificado do serviço de aplicativo no serviço de aplicativo](./media/configure-ssl-certificate/import-app-service-cert.png)

Selecione o certificado que você acabou de comprar e selecione **OK**.

Quando a operação for concluída, você verá o certificado na lista **certificados de chave privada** .

![Importação de certificado do serviço de aplicativo concluída](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Para proteger um domínio personalizado com esse certificado, você ainda precisa criar uma associação de certificado. Siga as etapas em [criar Associação](configure-ssl-bindings.md#create-binding).
>

## <a name="import-a-certificate-from-key-vault"></a>Importar um certificado de Key Vault

Se você usar Azure Key Vault para gerenciar seus certificados, poderá importar um certificado do PKCS12 do Key Vault para o serviço de aplicativo, desde que ele [atenda aos requisitos](#private-certificate-requirements).

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, no menu à esquerda, selecione **serviços de aplicativos** >  **\<nome-do-aplicativo >** .

No painel de navegação à esquerda do seu aplicativo, selecione **configurações de TLS/SSL** > **certificados de chave privada (. pfx)**  > **importar Key Vault certificado**.

![Importar Key Vault certificado no serviço de aplicativo](./media/configure-ssl-certificate/import-key-vault-cert.png))

Use a tabela a seguir para ajudá-lo a selecionar o certificado.

| Definição | Descrição |
|-|-|
| Subscrição | A assinatura à qual o Key Vault pertence. |
| Cofre de Chaves | O cofre com o certificado que você deseja importar. |
| Certificado | Selecione na lista de certificados do PKCS12 no cofre. Todos os certificados de PKCS12 no cofre são listados com suas impressões digitais, mas nem todos têm suporte no serviço de aplicativo. |

Quando a operação for concluída, você verá o certificado na lista **certificados de chave privada** . Se a importação falhar com um erro, o certificado não atenderá aos [requisitos do serviço de aplicativo](#private-certificate-requirements).

![Importação de Key Vault certificado concluída](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Para proteger um domínio personalizado com esse certificado, você ainda precisa criar uma associação de certificado. Siga as etapas em [criar Associação](configure-ssl-bindings.md#create-binding).
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

Se tiver gerado o pedido de certificado com OpenSSL, significa que criou um ficheiro de chave privada. Para exportar o certificado para PFX, execute o seguinte comando. Substitua os marcadores de posição  _&lt;private-key-file>_ e _&lt;merged-certificate-file>_ pelos caminhos para a chave privada e o ficheiro de certificado intercalado.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Quando lhe for pedido, defina uma palavra-passe de exportação. Vai utilizar esta palavra-passe ao carregar o certificado SSL para o Serviço de Aplicações mais tarde.

Se tiver utilizado o IIS ou _Certreq.exe_ para gerar o pedido de certificado, instale o certificado no seu computador local e [exporte-o para PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-certificate-to-app-service"></a>Carregar certificado no serviço de aplicativo

Agora você está pronto para carregar o certificado no serviço de aplicativo.

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, no menu à esquerda, selecione **serviços de aplicativos** >  **\<nome-do-aplicativo >** .

No painel de navegação à esquerda do seu aplicativo, selecione **configurações de TLS/SSL** > **certificados de chave privada (. pfx)**  > **carregar o certificado**.

![Carregar certificado privado no serviço de aplicativo](./media/configure-ssl-certificate/upload-private-cert.png)

Em **Ficheiro de Certificado PFX**, selecione o ficheiro PFX. Em **Palavra-passe do certificado**, escreva a palavra-passe que criou quando exportou o ficheiro PFX. Quando terminar, clique em **carregar**. 

Quando a operação for concluída, você verá o certificado na lista **certificados de chave privada** .

![Carregamento do certificado concluído](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Para proteger um domínio personalizado com esse certificado, você ainda precisa criar uma associação de certificado. Siga as etapas em [criar Associação](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-public-certificate"></a>Carregar um certificado público

Há suporte para certificados públicos no formato *. cer* . 

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, no menu à esquerda, selecione **serviços de aplicativos** >  **\<nome-do-aplicativo >** .

No painel de navegação à esquerda do seu aplicativo, clique em **configurações de TLS/SSL** > **certificados públicos (. cer)**  > **carregar o certificado de chave pública**.

Em **nome**, digite um nome para o certificado. No **arquivo de certificado cer**, selecione o arquivo cer.

Clique em **Carregar**.

![Carregar certificado público no serviço de aplicativo](./media/configure-ssl-certificate/upload-public-cert.png)

Depois que o certificado for carregado, copie a impressão digital do certificado e veja [tornar o certificado acessível](configure-ssl-certificate-in-code.md#load-the-certificate).

## <a name="manage-app-service-certificates"></a>Gerenciar certificados do serviço de aplicativo

Esta seção mostra como gerenciar um certificado do serviço de aplicativo adquirido em [importar um certificado do serviço de aplicativo](#import-an-app-service-certificate).

- [Certificado de rechaveamento](#rekey-certificate)
- [Renovar certificado](#renew-certificate)
- [Exportar certificado](#export-certificate)
- [Excluir certificado](#delete-certificate)

### <a name="rekey-certificate"></a>Certificado de rechaveamento

Se você considerar que a chave privada do certificado está comprometida, poderá refazer o seu certificado. Selecione o certificado na página [certificados do serviço de aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e, em seguida, selecione **rechaveamento e sincronização** no painel de navegação esquerdo.

Clique em **rechaveamento** para iniciar o processo. Esse processo pode levar de 1-10 a minutos para ser concluído.

![Rechaveamento de um certificado do serviço de aplicativo](./media/configure-ssl-certificate/rekey-app-service-cert.png)

A recriação de chaves do certificado reverte o certificado com um novo certificado emitido pela autoridade de certificação.

Quando a operação de rechaveamento for concluída, clique em **sincronizar**. A operação de sincronização atualiza automaticamente as associações de nome de host para o certificado no serviço de aplicativo sem causar nenhum tempo de inatividade para seus aplicativos.

> [!NOTE]
> Se você não clicar em **sincronizar**, o serviço de aplicativo sincronizará automaticamente seu certificado dentro de 48 horas.

### <a name="renew-certificate"></a>Renovar certificado

Para ativar a renovação automática de seu certificado a qualquer momento, selecione o certificado na página [certificados do serviço de aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e clique em **configurações de renovação automática** no painel de navegação esquerdo. Por padrão, os certificados do serviço de aplicativo têm um período de validade de um ano.

Selecione **ativado** e clique em **salvar**. Os certificados podem começar a renovar automaticamente 60 dias antes da expiração se você tiver a renovação automática ativada.

![Renovar certificado do serviço de aplicativo automaticamente](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Para renovar manualmente o certificado, clique em **renovação manual**. Você pode solicitar para renovar manualmente o certificado 60 dias antes da expiração.

Quando a operação de renovação for concluída, clique em **sincronizar**. A operação de sincronização atualiza automaticamente as associações de nome de host para o certificado no serviço de aplicativo sem causar nenhum tempo de inatividade para seus aplicativos.

> [!NOTE]
> Se você não clicar em **sincronizar**, o serviço de aplicativo sincronizará automaticamente seu certificado dentro de 48 horas.

### <a name="export-certificate"></a>Exportar o certificado

Como os certificados do serviço de aplicativo são um [segredo Key Vault](../key-vault/about-keys-secrets-and-certificates.md#key-vault-secrets), você pode exportar uma cópia pfx dele e usá-lo para outros serviços do Azure ou fora do Azure.

Para exportar o Certificado do Serviço de Aplicativo como um arquivo PFX, execute os comandos a seguir na [Cloud Shell](https://shell.azure.com). Você também pode executá-lo localmente se tiver [instalado CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Substitua os espaços reservados pelos nomes usados quando você [criou o certificado do serviço de aplicativo](#start-certificate-order).

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

O arquivo *appservicecertificate. pfx* baixado é um arquivo PKCS12 bruto que contém os certificados públicos e privados. Sempre que for solicitado, a senha de importação e a frase secreta de PEM serão a cadeia de caracteres vazia.

### <a name="delete-certificate"></a>Excluir certificado 

A exclusão de um certificado do serviço de aplicativo é final e irreversível. Qualquer associação no serviço de aplicativo com esse certificado torna-se inválida. Para evitar a exclusão acidental, o Azure coloca um bloqueio no certificado. Para excluir um certificado do serviço de aplicativo, primeiro você deve remover o bloqueio de exclusão no certificado.

Selecione o certificado na página [certificados do serviço de aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e selecione **bloqueios** no painel de navegação esquerdo.

Localize o bloqueio em seu certificado com a **exclusão**do tipo de bloqueio. À direita dele, selecione **excluir**.

![Excluir bloqueio para o certificado do serviço de aplicativo](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Agora você pode excluir o certificado do serviço de aplicativo. No painel de navegação esquerdo, selecione **visão geral** > **excluir**. Na caixa de diálogo de confirmação, digite o nome do certificado e selecione **OK**.

## <a name="automate-with-scripts"></a>Automatizar com scripts

### <a name="azure-cli"></a>CLI do Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Mais recursos

* [Proteger um nome DNS personalizado com uma associação SSL](configure-ssl-bindings.md)
* [Impor HTTPS](configure-ssl-bindings.md#enforce-https)
* [Impor o TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Usar um certificado SSL no código do aplicativo](configure-ssl-certificate-in-code.md)
* [Perguntas frequentes: certificados do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
