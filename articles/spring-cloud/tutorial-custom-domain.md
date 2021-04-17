---
title: 'Tutorial: Mapear um domínio personalizado existente para Azure Spring Cloud'
description: Como mapear um nome de serviço de nome distribuído personalizado (DNS) existente para Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 493752a3857b80b43668b6bf1b20480604442955
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568000"
---
# <a name="tutorial-map-an-existing-custom-domain-to-azure-spring-cloud"></a>Tutorial: Mapear um domínio personalizado existente para Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

O Serviço de Nome de Domínio (DNS) é uma técnica para armazenar nomes de nó de rede em toda uma rede. Este tutorial mapeia um domínio, como www.contoso.com, usando um registo CNAME. Protege o domínio personalizado com um certificado e mostra como impor a Segurança da Camada de Transporte (TLS), também conhecida como Camada de Tomadas Seguras (SSL). 

Os certificados encriptam o tráfego web. Estes certificados TLS/SSL podem ser armazenados no Cofre da Chave Azure. 

## <a name="prerequisites"></a>Pré-requisitos
* Uma aplicação implementada para Azure Spring Cloud (ver [Quickstart: Lançar uma aplicação existente em Azure Spring Cloud utilizando o portal Azure](spring-cloud-quickstart.md), ou usar uma aplicação existente).
* Um nome de domínio com acesso ao registo DNS para provedor de domínio, como o GoDaddy.
* Um certificado privado (isto é, o seu certificado auto-assinado) de um fornecedor de terceiros. O certificado deve coincidir com o domínio.
* Um exemplo implantado de [Azure Key Vault](../key-vault/general/overview.md)

## <a name="keyvault-private-link-considerations"></a>Considerações de Ligação Privada Keyvault

Os IPs de gestão da Azure Spring Cloud ainda não fazem parte dos serviços da Azure Trusted Microsoft. Portanto, para permitir que a Azure Spring Cloud carregue certificados de um Cofre-Chave protegido com ligações de ponto final privado, deve adicionar os seguintes IPs à Firewall Azure Key Vault:

```
20.53.123.160 52.143.241.210 40.65.234.114 52.142.20.14 20.54.40.121 40.80.210.49 52.253.84.152 20.49.137.168 40.74.8.134 51.143.48.243
```

## <a name="import-certificate"></a>Importar o certificado
### <a name="prepare-your-certificate-file-in-pfx-optional"></a>Prepare o seu arquivo de certificado em PFX (opcional)
Suporte Azure Key Vault que importa certificado privado em formato PEM e PFX. Se o ficheiro PEM que obteve do seu fornecedor de certificados não funcionar na secção seguinte: Guarde o [certificado no Key Vault,](#save-certificate-in-key-vault)siga os passos aqui para gerar um PFX para Azure Key Vault.

#### <a name="merge-intermediate-certificates"></a>Intercalar certificados intermédios

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

#### <a name="export-certificate-to-pfx"></a>Exportar o certificado para PFX

Exporte o seu certificado TLS/SSL fundido com a chave privada com a qual o seu pedido de certificado foi gerado.

Se tiver gerado o pedido de certificado com OpenSSL, significa que criou um ficheiro de chave privada. Para exportar o certificado para PFX, execute o seguinte comando. Substitua os espaços reservados>de _&lt; ficheiros de chave privada_ e>de ficheiro de certificado _&lt; fundido_ pelos caminhos da sua chave privada e do seu ficheiro de certificado fundido.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Quando lhe for pedido, defina uma palavra-passe de exportação. Utilizará esta palavra-passe ao enviar o certificado TLS/SSL para o Cofre da Chave Azure mais tarde.

Se tiver utilizado o IIS ou _Certreq.exe_ para gerar o pedido de certificado, instale o certificado no seu computador local e [exporte-o para PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="save-certificate-in-key-vault"></a>Guardar certificado no Cofre de Chaves
O procedimento para importar um certificado requer que o ficheiro codificado PEM ou PFX esteja no disco e deve ter a chave privada. 
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Para fazer o upload do seu certificado para o cofre da chave:
1. Vá para o seu caso do cofre chave.
1. No painel de navegação à esquerda, clique em **Certificados.**
1. No menu superior, clique em **Gerar/importar.**
1. No **Criar um** diálogo de certificado ao abrigo **do Método de criação de certificados,** selecione `Import` .
1. No **Ficheiro de Certificado de Upload,** navegue para a localização do certificado e selecione-o.
1. Em **Password,** insira a chave privada do seu certificado.
1. Clique em **Criar**.

    ![Certificado de importação 1](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```
---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>Grant Azure Spring Cloud acesso ao seu cofre chave

Você precisa conceder ao Azure Spring Cloud acesso ao seu cofre chave antes de importar certificado:
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Vá para o seu caso do cofre chave.
1. No painel de navegação à esquerda, clique em **Access Police**.
1. No menu superior, clique em **Adicionar Política de Acesso.**
1. Preencha a informação e clique no botão **Adicionar** e, em seguida, **guarde a** polícia de acesso.

| Permissão secreta | Permissão de certificado | Selecione principal |
|--|--|--|
| Get, List | Get, List | Azure Spring Cloud Domain-Management |

![Certificado de importação 2](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Grant Azure Spring Cloud leia o acesso ao cofre da chave, substitua `<key vault resource group>` o e no seguinte `<key vault name>` comando.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
``` 
---

### <a name="import-certificate-to-azure-spring-cloud"></a>Certificado de importação para Azure Spring Cloud
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Vá para o seu caso de serviço. 
1. A partir do painel de navegação esquerdo da sua aplicação, selecione **as definições TLS/SSL**.
1. Em seguida, clique no **Certificado de Cofre de Chave de Importação**.

    ![Importar o certificado](./media/custom-dns-tutorial/import-certificate.png)

1. Quando tiver importado com sucesso o seu certificado, verá-o na lista de **Certificados-Chave Privados.**

    ![Certificado chave privado](./media/custom-dns-tutorial/key-certificates.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

Para apresentar uma lista de certificados importados:

```
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```
---

> [!IMPORTANT] 
> Para garantir um domínio personalizado com este certificado, ainda precisa de ligar o certificado a um domínio específico. Siga os passos nesta secção: [Adicionar Encadernação SSL](#add-ssl-binding).

## <a name="add-custom-domain"></a>Adicionar domínio personalizado
Você pode usar um registo CNAME para mapear um nome DNS personalizado para Azure Spring Cloud. 

> [!NOTE] 
> O registo A não é suportado. 

### <a name="create-the-cname-record"></a>Criar o registo CNAME
Vá ao seu fornecedor DNS e adicione um registo CNAME para mapear o seu domínio para o <service_name>.azuremicroservices.io. Aqui <service_name> é o nome do seu exemplo de Azure Spring Cloud. Apoiamos o domínio wildcard e o sub domínio. Depois de adicionar o CNAME, a página de registos DNS assemelhar-se-á ao seguinte exemplo: 

![Página de registos DNS](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Mapear o seu domínio personalizado para a aplicação Azure Spring Cloud
Se não tiver uma aplicação em Azure Spring Cloud, siga as instruções em [Quickstart: Lance uma aplicação existente em Azure Spring Cloud utilizando o portal Azure](./spring-cloud-quickstart.md).

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Vá para a página de inscrição.

1. Selecione **domínio personalizado**.
2. Em seguida, **adicione domínio personalizado**. 

    ![Domínio personalizado](./media/custom-dns-tutorial/custom-domain.png)

3. Digite o nome de domínio totalmente qualificado para o qual adicionou um registo CNAME, como www.contoso.com. Certifique-se de que o tipo de gravação do Hostname está definido para CNAME (<service_name>.azuremicroservices.io)
4. Clique **em Validar** para ativar o botão **Adicionar.**
5. Clique em **Adicionar**.

    ![Adicionar domínio personalizado](./media/custom-dns-tutorial/add-custom-domain.png)

Uma aplicação pode ter vários domínios, mas um domínio só pode mapear para uma aplicação. Quando tiver mapeado com sucesso o seu domínio personalizado para a aplicação, irá vê-lo na tabela de domínio personalizado.

![Tabela de domínio personalizado](./media/custom-dns-tutorial/custom-domain-table.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

Para mostrar a lista de domínios personalizados:
```
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```
---

> [!NOTE]
> Uma etiqueta **Não Segura** para o seu domínio personalizado significa que ainda não está vinculado a um certificado SSL. Qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá um erro ou aviso.

## <a name="add-ssl-binding"></a>Adicionar ligação SSL

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Na tabela de domínio personalizado, **selecione Adicionar a ligação ssl** como mostrado na figura anterior.  
1. Selecione o seu **Certificado** ou importe-o.
1. Clique em **Guardar**.

    ![Adicionar encadernação SSL 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```
---

Depois de adicionar com sucesso a ligação SSL, o estado de domínio estará seguro: **Saudável**. 

![Adicionar encadernação SSL 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Impor HTTPS
Por predefinição, qualquer pessoa ainda pode aceder à sua aplicação utilizando HTTP, mas pode redirecionar todos os pedidos HTTP PARA a porta HTTPS.
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Na sua página de aplicações, na navegação à esquerda, selecione **Domínio Personalizado**. Em seguida, definir **HTTPS Apenas,** para *Verdadeiro*.

![Adicionar ligação SSL 3](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```
---
Quando a operação estiver concluída, navegue para qualquer um dos URLs HTTPS que apontam para a sua aplicação. Note que os URLs HTTP não funcionam.

## <a name="see-also"></a>Ver também
* [O que é o Azure Key Vault?](../key-vault/general/overview.md)
* [Importar um certificado](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [Lance a sua App Cloud Spring utilizando o Azure CLI](./spring-cloud-quickstart.md)
