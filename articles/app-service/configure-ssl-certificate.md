---
title: Adicionar e gerir certificados SSL
description: Crie um certificado gratuito, importe um certificado de Serviço app, importe um certificado Key Vault ou compre um certificado de Serviço de Aplicações no Serviço de Aplicações Azure.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 120caf459a7a8ca4e60d5e447a1e4130c0bce389
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356905"
---
# <a name="add-an-ssl-certificate-in-azure-app-service"></a>Adicione um certificado SSL no Serviço de Aplicações Azure

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este artigo mostra-lhe como criar, carregar ou importar um certificado privado ou um certificado público para o Serviço de Aplicações. 

Assim que o certificado for adicionado à sua aplicação ou [aplicação de função](https://docs.microsoft.com/azure/azure-functions/)do App Service, pode [garantir um nome DNS personalizado com ele](configure-ssl-bindings.md) ou [usá-lo no seu código](configure-ssl-certificate-in-code.md)de aplicação .

A tabela que se segue lista as opções que tem para adicionar certificados no Serviço de Aplicações:

|Opção|Descrição|
|-|-|
| Criar um Certificado gerido por serviço de aplicações gratuito (Pré-visualização) | Um certificado privado que é fácil de usar se apenas precisar de garantir o seu [domínio personalizado](app-service-web-tutorial-custom-domain.md) `www` ou qualquer domínio não nu no Serviço de Aplicações. |
| Comprar um certificado de serviço de aplicações | Um certificado privado que é gerido pelo Azure. Combina a simplicidade da gestão automatizada de certificados e a flexibilidade das opções de renovação e exportação. |
| Importar um certificado da Key Vault | Útil se utilizar o [Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/) para gerir os seus [certificados PKCS12](https://wikipedia.org/wiki/PKCS_12). Ver [requisitos de certificado privado](#private-certificate-requirements). |
| Faça upload de um certificado privado | Se já tem um certificado privado de um fornecedor de terceiros, pode carregá-lo. Ver [requisitos de certificado privado](#private-certificate-requirements). |
| Faça upload de um certificado público | Os certificados públicos não são usados para proteger domínios personalizados, mas pode carregá-los no seu código se precisar deles para aceder a recursos remotos. |

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia:

- [Criar uma aplicação de Serviço de Aplicações.](/azure/app-service/)
- Certificado gratuito apenas: mapear um subdomínio (por exemplo, `www.contoso.com`) para o Serviço de Aplicações com um [registo CNAME](app-service-web-tutorial-custom-domain.md#map-a-cname-record).

## <a name="private-certificate-requirements"></a>Requisitos de certificados privados

> [!NOTE]
> As Aplicações Web Azure **não** suportam o AES256 e todos os ficheiros pfx devem ser encriptados com TripleDES.

O Certificado Gerido pelo Serviço de [Aplicações gratuito](#create-a-free-certificate-preview) ou o certificado de Serviço de [Aplicações](#import-an-app-service-certificate) já satisfazem os requisitos do Serviço de Aplicações. Se optar por fazer o upload ou importar um certificado privado para o Serviço de Aplicações, o seu certificado deve satisfazer os seguintes requisitos:

* Exportado como um ficheiro PFX protegido por [palavra-passe](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)
* Conter uma chave privada com, pelo menos, 2048 bits de comprimento
* Conter todos os certificados intermédios na cadeia de certificados

Para garantir um domínio personalizado numa ligação SSL, o certificado tem requisitos adicionais:

* Contém uma [utilização de chave estendida](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) para autenticação do servidor (OID = 1.3.6.1.5.5.7.3.1)
* Ser assinado por uma autoridade de certificação fidedigna

> [!NOTE]
> Os **certificados de criptografia de curva elíptica (ECC)** podem funcionar com o Serviço de Aplicações, mas não são abordados neste artigo. Trabalhe com a sua autoridade de certificados para saber quais são os passos exatos para criar certificados ECC.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Criar um certificado gratuito (Pré-visualização)

O Certificado Gerido pelo Serviço de Aplicações gratuito é uma solução chave-na-mão para garantir o seu nome DNS personalizado no Serviço de Aplicações. É um certificado SSL totalmente funcional que é gerido pelo App Service e renovado automaticamente. O certificado gratuito vem com as seguintes limitações:

- Não suporta certificados wildcard.
- Não suporta domínios nus.
- Não é exportável.
- Não suporta registos DNS A.

> [!NOTE]
> O certificado gratuito é emitido pela DigiCert. Para alguns domínios de alto nível, deve permitir explicitamente a DigiCert como emitente de certificado, criando um registo de [domínio CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) com o valor: `0 issue digicert.com`.
> 

Para criar um Certificado gerido por serviço de aplicações gratuito:

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **App Services** >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, selecione **definições TLS/SSL** > **Certificados de Chave Privadas (.pfx)**  > Criar Certificado Gerido pelo Serviço de **Aplicações**.

![Criar certificado gratuito no Serviço de Aplicações](./media/configure-ssl-certificate/create-free-cert.png)

Qualquer domínio não-nu devidamente mapeado para a sua aplicação com um registo CNAME está listado no diálogo. Selecione o domínio personalizado para criar um certificado gratuito e selecione **Criar**. Pode criar apenas um certificado para cada domínio personalizado suportado.

Quando a operação estiver concluída, consulte o certificado na lista de **Certificados de Chave Privada.**

![Criar certificado gratuito terminado](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Para garantir um domínio personalizado com este certificado, ainda precisa de criar um certificado vinculativo. Siga os passos em [Criar encadernação.](configure-ssl-bindings.md#create-binding)
>

## <a name="import-an-app-service-certificate"></a>Importar um Certificado de Serviço de Aplicações

Se comprar um Certificado de Serviço de Aplicações à Azure, o Azure gere as seguintes tarefas:

- Cuida do processo de compra do GoDaddy.
- Realiza verificação de domínio do certificado.
- Mantém o certificado no [Cofre de Chaves Azure.](../key-vault/key-vault-overview.md)
- Gere a renovação do certificado (ver [Certificado Renovar).](#renew-certificate)
- Sincronizar automaticamente o certificado com as cópias importadas nas aplicações do App Service.

Para comprar um certificado de Serviço de Aplicações, vá iniciar a encomenda de [certificado](#start-certificate-order).

Se já tem um certificado de serviço de aplicações em funcionamento, pode:

- Importar o certificado para o Serviço de [Aplicações.](#import-certificate-into-app-service)
- [Gerir o certificado](#manage-app-service-certificates), como renovar, rekey e exportá-lo.

### <a name="start-certificate-order"></a>Iniciar a encomenda de certificado

Inicie uma encomenda de certificado de serviço de aplicações no Certificado de Serviço de <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">Aplicações criar página</a>.

![Iniciar a compra de certificado de serviço de aplicações](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Utilize a tabela seguinte para o ajudar a configurar o certificado. Quando terminar, clique em **Criar**.

| Definição | Descrição |
|-|-|
| Nome | Um nome amigável para o seu certificado de Serviço de Aplicações. |
| Nome do anfitrião do domínio nu | Especifique o domínio raiz aqui. O certificado emitido assegura tanto o domínio radicular *como* o subdomínio `www`. No certificado emitido, o campo Nome Comum contém o domínio raiz, e o campo Nome Alternativo sujeito contém o domínio `www`. Para fixar apenas qualquer subdomínio, especifique aqui o nome de domínio totalmente qualificado do subdomínio (por exemplo, `mysubdomain.contoso.com`).|
| Subscrição | A subscrição que conterá o certificado. |
| Grupo de recursos | O grupo de recursos que conterá o certificado. Pode utilizar um novo grupo de recursos ou selecionar o mesmo grupo de recursos que a sua app App Service, por exemplo. |
| Certificado SKU | Determina o tipo de certificado a criar, seja um certificado padrão ou um [certificado wildcard.](https://wikipedia.org/wiki/Wildcard_certificate) |
| Termos Legais | Clique para confirmar que concorda com os termos legais. Os certificados são obtidos do GoDaddy. |

### <a name="store-in-azure-key-vault"></a>Loja em Cofre chave Azure

Uma vez concluído o processo de compra do certificado, há mais alguns passos que precisa de completar antes de começar a usar este certificado. 

Selecione o certificado na página de Certificados de Serviço de [Aplicações](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e, em seguida, clique na **configuração** do certificado > **Passo 1: Armazenar**.

![Configure armazenamento chave vault do certificado de serviço de aplicações](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) é um serviço Azure que ajuda a salvaguardar chaves criptográficas e segredos usados por aplicações e serviços em nuvem. É o armazenamento de eleição para certificados de serviço de aplicações.

Na página **key vault status,** clique no **Repositório key vault** para criar um novo cofre ou escolha um cofre existente. Se optar por criar um novo cofre, utilize a tabela seguinte para o ajudar a configurar o cofre e clique em Criar. Crie o novo Key Vault dentro do mesmo grupo de subscrição e recursos que a sua app App Service.

| Definição | Descrição |
|-|-|
| Nome | Um nome único que consiste em caracteres alfanuméricos e traços. |
| Grupo de recursos | Como recomendação, selecione o mesmo grupo de recursos que o seu certificado de Serviço de Aplicações. |
| Localização | Selecione o mesmo local que a sua aplicação App Service. |
| Escalão de preço | Para obter informações, consulte os detalhes dos [preços do Cofre chave Azure](https://azure.microsoft.com/pricing/details/key-vault/). |
| Políticas de acesso| Define as aplicações e o acesso permitido aos recursos do cofre. Pode configurá-lo mais tarde, seguindo os passos da [Grant várias aplicações acesso a um cofre chave](../key-vault/key-vault-group-permissions-for-apps.md). |
| Acesso à Rede Virtual | Restringir o acesso ao cofre a certas redes virtuais azure. Pode configurá-lo mais tarde, seguindo os passos em [Configurar Firewalls e Redes Virtuais](../key-vault/key-vault-network-security.md) |

Assim que escolhero cofre, feche a página de **Repositório do Cofre chave.** O **passo 1: A** opção loja deve mostrar uma marca de verificação verde para o sucesso. Mantenha a página aberta para o próximo passo.

### <a name="verify-domain-ownership"></a>Verificar a propriedade do domínio

A partir da mesma página de **Configuração** de Certificado que usou no último passo, clique no **passo 2: Verificar**.

![Verificar domínio para certificado de serviço de aplicações](./media/configure-ssl-certificate/verify-domain.png)

Selecione Verificação de **Serviço de Aplicações**. Uma vez que já mapeou o domínio para a sua aplicação web (ver [Pré-requisitos),](#prerequisites)já está verificado. Basta clicar **em Verificar** para terminar este passo. Clique no botão **'Actualizar'** até aparecer o Certificado de mensagem Verificado por **domínio.**

> [!NOTE]
> São apoiados quatro tipos de métodos de verificação de domínios: 
> 
> - Serviço de **Aplicações** - A opção mais conveniente quando o domínio já está mapeado para uma aplicação do App Service na mesma subscrição. Aproveita o facto de a aplicação app Service já ter verificado a propriedade do domínio.
> - **Domínio** - Verifique um domínio do Serviço de [Aplicações que adquiriu ao Azure](manage-custom-dns-buy-domain.md). O Azure adiciona automaticamente o registo TXT de verificação para si e completa o processo.
> - **Correio** - Verifique o domínio enviando um e-mail ao administrador de domínio. As instruções são fornecidas quando selecionar a opção.
> - **Manual** - Verifique o domínio utilizando uma página HTML (apenas um certificado**Padrão)** ou um registo DNS TXT. As instruções são fornecidas quando selecionar a opção.

### <a name="import-certificate-into-app-service"></a>Certificado de importação em Serviço de Aplicações

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **App Services** >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, selecione **definições TLS/SSL** > **Certificados de Chave Privadas (.pfx)**  > Certificado de Serviço de **Aplicações de Importação**.

![Certificado de serviço de aplicações de importação no Serviço de Aplicações](./media/configure-ssl-certificate/import-app-service-cert.png)

Selecione o certificado que acabou de comprar e selecione **OK**.

Quando a operação estiver concluída, consulte o certificado na lista de **Certificados de Chave Privada.**

![Certificado de Serviço de Aplicações de Importação terminado](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Para garantir um domínio personalizado com este certificado, ainda precisa de criar um certificado vinculativo. Siga os passos em [Criar encadernação.](configure-ssl-bindings.md#create-binding)
>

## <a name="import-a-certificate-from-key-vault"></a>Importar um certificado da Key Vault

Se utilizar o Cofre de Chaves Azure para gerir os seus certificados, pode importar um certificado PKCS12 do Key Vault para o Serviço de Aplicações, desde que [satisfaça os requisitos](#private-certificate-requirements).

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **App Services** >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, selecione **as definições TLS/SSL** > **Certificados de Chave Privadas (.pfx)**  > Certificado de Cofre de **Chave de Importação**.

![Certificado de cofre de chave de importação no serviço de aplicações](./media/configure-ssl-certificate/import-key-vault-cert.png)

Utilize a tabela seguinte para o ajudar a selecionar o certificado.

| Definição | Descrição |
|-|-|
| Subscrição | A assinatura a que pertence o Cofre Chave. |
| Cofre de Chaves | O cofre com o certificado que quer importar. |
| Certificado | Selecione na lista de certificados PKCS12 no cofre. Todos os certificados PKCS12 no cofre estão listados com as suas impressões digitais, mas nem todos são suportados no Serviço de Aplicações. |

Quando a operação estiver concluída, consulte o certificado na lista de **Certificados de Chave Privada.** Se a importação falhar com um erro, o certificado não satisfaz os [requisitos para](#private-certificate-requirements)o Serviço de Aplicações .

![Certificado de cofre chave de importação terminado](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Para garantir um domínio personalizado com este certificado, ainda precisa de criar um certificado vinculativo. Siga os passos em [Criar encadernação.](configure-ssl-bindings.md#create-binding)
>

## <a name="upload-a-private-certificate"></a>Faça upload de um certificado privado

Assim que obtiver um certificado do seu fornecedor de certificados, siga os passos nesta secção para o preparar para o Serviço de Aplicações.

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

### <a name="upload-certificate-to-app-service"></a>Enviar certificado para o Serviço de Aplicações

Está agora pronto para carregar o certificado para o Serviço de Aplicações.

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **App Services** >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, selecione **definições TLS/SSL** > **Certificados de Chave Privadas (.pfx)**  > **Upload Certificate**.

![Faça upload de certificado privado no Serviço de Aplicações](./media/configure-ssl-certificate/upload-private-cert.png)

Em **Ficheiro de Certificado PFX**, selecione o ficheiro PFX. Em **Palavra-passe do certificado**, escreva a palavra-passe que criou quando exportou o ficheiro PFX. Quando terminar, clique em **Carregar**. 

Quando a operação estiver concluída, consulte o certificado na lista de **Certificados de Chave Privada.**

![Certificado de upload terminado](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Para garantir um domínio personalizado com este certificado, ainda precisa de criar um certificado vinculativo. Siga os passos em [Criar encadernação.](configure-ssl-bindings.md#create-binding)
>

## <a name="upload-a-public-certificate"></a>Faça upload de um certificado público

Os certificados públicos são suportados no formato *.cer.* 

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **App Services** >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, clique nas **definições tLS/SSL** > **Certificados Públicos (.cer)**  > **Carregar Certificado**de Chave Pública .

Em **Nome,** digite um nome para o certificado. No **ficheiro Cer Certificate,** selecione o ficheiro CER.

Clique em **Carregar**.

![Faça upload de certificado público no Serviço de Aplicações](./media/configure-ssl-certificate/upload-public-cert.png)

Uma vez carregado o certificado, copie a impressão digital do certificado e consulte [Tornar o certificado acessível](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>Gerir certificados de serviço de aplicações

Esta secção mostra-lhe como gerir um certificado de Serviço app que adquiriu na Importação de um certificado de Serviço de [Aplicações](#import-an-app-service-certificate).

- [Certificado de rechave](#rekey-certificate)
- [Renovar certificado](#renew-certificate)
- [Certificado de exportação](#export-certificate)
- [Excluir certificado](#delete-certificate)

### <a name="rekey-certificate"></a>Certificado de rechave

Se acha que a chave privada do seu certificado está comprometida, pode re-chave o seu certificado. Selecione o certificado na página de [Certificados](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) de Serviço de Aplicações e, em seguida, selecione **Rekey e Sync** da navegação esquerda.

Clique em **Rekey** para iniciar o processo. Este processo pode levar 1-10 minutos para ser concluído.

![Rekey um certificado de serviço de aplicações](./media/configure-ssl-certificate/rekey-app-service-cert.png)

Resqueifique o certificado, o certificado passa o certificado com um novo certificado emitido pela autoridade do certificado.

Assim que o funcionamento da chave estiver concluído, clique em **Sync**. A operação de sincronização atualiza automaticamente as encadernações do nome de anfitrião para o certificado no Serviço de Aplicações sem causar qualquer inatividade nas suas apps.

> [!NOTE]
> Se não clicar em **Sync,** o Serviço de Aplicações sincroniza automaticamente o seu certificado dentro de 48 horas.

### <a name="renew-certificate"></a>Renovar certificado

Para ativar a renovação automática do seu certificado a qualquer momento, selecione o certificado na página de [Certificados](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) de Serviço de Aplicações e, em seguida, clique em Definições de **Renovação Automática** na navegação à esquerda. Por padrão, os Certificados de Serviço de Aplicações têm um período de validade de um ano.

Selecione **On** e clique em **Guardar**. Os certificados podem começar a renovar automaticamente 60 dias antes da expiração se tiver a renovação automática ligada.

![Renovar automaticamente o certificado de Serviço de Aplicações](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Para renovar manualmente o certificado, clique em **Renovar Manual**. Pode solicitar a renovação manual do seu certificado 60 dias antes da expiração.

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

O ficheiro *appservicecertificate.pfx* descarregado é um ficheiro PKCS12 cru que contém certificados públicos e privados. Em cada pedido, utilize uma corda vazia para a palavra-passe de importação e a frase de passe PEM.

### <a name="delete-certificate"></a>Excluir certificado 

A eliminação de um certificado de Serviço de Aplicações é definitiva e irreversível. A eliminação de um recurso do Certificado de Serviço de Aplicações resulta na revogação do certificado. Qualquer encadernação no Serviço de Aplicações com este certificado torna-se inválida. Para evitar a supressão acidental, o Azure coloca um cadeado no certificado. Para eliminar um certificado de Serviço de Aplicações, tem primeiro de remover o bloqueio de eliminação do certificado.

Selecione o certificado na página de Certificados de Serviço de [Aplicações](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e, em seguida, selecione **Locks** na navegação à esquerda.

Encontre o cadeado no seu certificado com o tipo de bloqueio **Delete**. À direita, selecione **Delete**.

![Eliminar o bloqueio para o certificado de serviço de aplicações](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Agora pode eliminar o certificado de Serviço de Aplicações. A partir da navegação à esquerda, selecione **Visão geral** > **Eliminar**. No diálogo de confirmação, escreva o nome do certificado e selecione **OK**.

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
