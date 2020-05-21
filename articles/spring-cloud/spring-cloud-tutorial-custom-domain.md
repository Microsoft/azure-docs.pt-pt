---
title: Tutorial - Mapeie um domínio personalizado existente para azure Spring Cloud
description: Como mapear um nome personalizado do Serviço de Nomes Distribuídos (DNS) existente para Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.openlocfilehash: 19ccdf85e1753bea202c5c157919ab4e8ff96d06
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660252"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Mapeie um domínio personalizado existente para azure Spring Cloud
Serviço de Nomedistribuído (DNS) é uma técnica para armazenar nomes de nó de rede em toda uma rede. Este tutorial mapeia um domínio, como www.contoso.com, usando um registo CNAME. Ele protege o domínio personalizado com um certificado e mostra como impor a Segurança da Camada de Transporte (TLS), também conhecida como Camada de Tomadas Seguras (SSL). 

Os certificados encriptam o tráfego web. Estes certificados TLS/SSL podem ser armazenados no Cofre de Chaves Azure. 

## <a name="prerequisites"></a>Pré-requisitos
* Uma aplicação implementada para azure Spring Cloud (ver [Quickstart: Launch a existente aplicação Azure Spring Cloud utilizando o portal Azure](spring-cloud-quickstart-launch-app-portal.md), ou utilizar uma aplicação existente).
* Um nome de domínio com acesso ao registo DNS para fornecedor de domínio, como goDaddy.
* Um certificado privado (isto é, o seu certificado auto-assinado) de um fornecedor de terceiros. O certificado deve corresponder ao domínio.
* Uma instância implantada de [Cofre chave Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

## <a name="import-certificate"></a>Importar o certificado 
O procedimento para importar um certificado requer que o ficheiro codificado PEM ou PFX esteja em disco e deve ter a chave privada. 

Para fazer o upload do seu certificado para o cofre da chave:
1. Vá ao seu cofre chave.
1. No painel de navegação à esquerda, clique em **Certificados**.
1. No menu superior, clique **em Gerar/importar**.
1. No **Diálogo Criar um certificado** ao abrigo **do método de criação de certificado,** selecione `Import` .
1. Sob **o ficheiro de certificado de upload,** navegue para a localização do certificado e selecione-o.
1. Em **Password,** introduza a chave privada para o seu certificado.
1. Clique em **Criar**.

    ![Certificado de importação 1](./media/custom-dns-tutorial/import-certificate-a.png)

Para importar certificado para azure Spring Cloud:
1. Vá para a sua instância de serviço. 
1. A partir do painel de navegação esquerdo da sua aplicação, selecione **as definições TLS/SSL**.
1. Em seguida, clique no Certificado de Cofre de **Chave de Importação**.

    ![Importar o certificado](./media/custom-dns-tutorial/import-certificate.png)

Ou, pode utilizar o Azure CLI para importar o certificado:

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

> [!IMPORTANT] 
> Certifique-se de que concede acesso à Nuvem de primavera Azure ao seu cofre de chaves antes de executar o comando de certificado de importação anterior. Se não o fez, pode executar o seguinte comando para conceder os direitos de acesso.

```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list
``` 

Quando tiver importado com sucesso o seu certificado, verá-o na lista de **Certificados de Chave Privada**.

![Certificado de chave privada](./media/custom-dns-tutorial/key-certificates.png)

Ou, pode utilizar o Azure CLI para mostrar uma lista de certificados:

```
az spring-cloud certificate list
```

> [!IMPORTANT] 
> Para garantir um domínio personalizado com este certificado, ainda precisa de ligar o certificado a um domínio específico. Siga os passos deste documento sob a rubrica **Adicionar encadernação SSL**.

## <a name="add-custom-domain"></a>Adicionar domínio personalizado
Você pode usar um disco CNAME para mapear um nome DNS personalizado para Azure Spring Cloud. 

> [!NOTE] 
> O registo A não é suportado. 

### <a name="create-the-cname-record"></a>Criar o registo CNAME
Vá ao seu fornecedor DNS e adicione um disco CNAME para mapear o seu domínio para o <service_name>.azuremicroservices.io. Aqui <service_name> é o nome da sua instância Azure Spring Cloud. Apoiamos o domínio wildcard e sub domínio. Depois de adicionar o CNAME, a página de registos DNS assemelhar-se-á ao seguinte exemplo: 

![Página de registos DNS](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Mapeie o seu domínio personalizado para a aplicação Azure Spring Cloud
Se não tiver uma aplicação na Nuvem de primavera Azure, siga as instruções em [Quickstart: Lance uma aplicação Azure Spring Cloud existente utilizando o portal Azure](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master).

Vá para a página de inscrição.

1. Selecione **Domínio Personalizado**.
2. Em seguida, **adicione domínio personalizado**. 

    ![Domínio personalizado](./media/custom-dns-tutorial/custom-domain.png)

3. Digite o nome de domínio totalmente qualificado para o qual adicionou um registo CNAME, como www.contoso.com. Certifique-se de que o tipo de registo hostname está definido para CNAME (<service_name>.azuremicroservices.io)
4. Clique em **Validar** para ativar o botão **Adicionar.**
5. Clique em **Adicionar**.

    ![Adicionar domínio personalizado](./media/custom-dns-tutorial/add-custom-domain.png)

Ou, pode utilizar o Azure CLI para adicionar um domínio personalizado:
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> 
```

Uma aplicação pode ter vários domínios, mas um domínio só pode mapear para uma aplicação. Quando tiver mapeado com sucesso o seu domínio personalizado para a aplicação, verá-o na tabela de domínio personalizada.

![Tabela de domínio personalizada](./media/custom-dns-tutorial/custom-domain-table.png)

Ou, pode usar o Azure CLI para mostrar uma lista de domínios personalizados:
```
az spring-cloud app custom-domain list --app <app name> 
```

> [!NOTE]
> Uma etiqueta **Não Segura** para o seu domínio personalizado significa que ainda não está ligada a um certificado SSL. Qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá um erro ou aviso.

## <a name="add-ssl-binding"></a>Adicionar encadernação SSL
Na tabela de domínio personalizada, **selecione Adicionar encadernação ssl** como mostrado na figura anterior.  
1. Selecione o seu **Certificado** ou importe-o.
1. Clique em **Guardar**.

    ![Adicionar encadernação SSL](./media/custom-dns-tutorial/add-ssl-binding.png)

Ou, pode utilizar o AZURE CLI para **adicionar encadernação ssl:**
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> 
```

Depois de adicionar com sucesso a ligação SSL, o estado de domínio será seguro: **Saudável**. 

![Adicionar encadernação SSL](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Impor HTTPS
Por padrão, qualquer pessoa ainda pode aceder à sua aplicação usando HTTP, mas pode redirecionar todos os pedidos HTTP para a porta HTTPS.

Na página da sua aplicação, na navegação à esquerda, selecione **Custom Domain**. Em seguida, definir **HTTPS Only,** to *True*.

![Adicionar encadernação SSL](./media/custom-dns-tutorial/enforce-http.png)

Ou, pode utilizar o Azure CLI para impor HTTPS:
```
az spring-cloud app update -name <app-name> --https-only <true|false> -g <resource group> --service <service-name>
```

Quando a operação estiver concluída, navegue para qualquer um dos URLs HTTPS que apontem para a sua aplicação. Note que os URLs http não funcionam.

## <a name="see-also"></a>Ver também
* [O que é o cofre de chave do Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Importar um certificado](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Lance a sua App Spring Cloud utilizando o Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

