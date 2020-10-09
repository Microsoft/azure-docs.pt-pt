---
title: Tutorial - Mapear um domínio personalizado existente para Azure Spring Cloud
description: Como mapear um nome de serviço de nome distribuído personalizado (DNS) existente para Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 5892fd732a1e66b2b7dd4c1031cabfcbcc768c6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326155"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Mapear um domínio personalizado existente para Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

O Serviço de Nome de Domínio (DNS) é uma técnica para armazenar nomes de nó de rede em toda uma rede. Este tutorial mapeia um domínio, como www.contoso.com, usando um registo CNAME. Protege o domínio personalizado com um certificado e mostra como impor a Segurança da Camada de Transporte (TLS), também conhecida como Camada de Tomadas Seguras (SSL). 

Os certificados encriptam o tráfego web. Estes certificados TLS/SSL podem ser armazenados no Cofre da Chave Azure. 

## <a name="prerequisites"></a>Pré-requisitos
* Uma aplicação implementada para Azure Spring Cloud (ver [Quickstart: Lançar uma aplicação existente em Azure Spring Cloud utilizando o portal Azure](spring-cloud-quickstart.md), ou usar uma aplicação existente).
* Um nome de domínio com acesso ao registo DNS para provedor de domínio, como o GoDaddy.
* Um certificado privado (isto é, o seu certificado auto-assinado) de um fornecedor de terceiros. O certificado deve coincidir com o domínio.
* Um exemplo implantado de [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

## <a name="import-certificate"></a>Importar o certificado 
O procedimento para importar um certificado requer que o ficheiro codificado PEM ou PFX esteja no disco e deve ter a chave privada. 

Para fazer o upload do seu certificado para o cofre da chave:
1. Vá para o seu caso do cofre chave.
1. No painel de navegação à esquerda, clique em **Certificados.**
1. No menu superior, clique em **Gerar/importar.**
1. No **Criar um** diálogo de certificado ao abrigo **do Método de criação de certificados,** selecione `Import` .
1. No **Ficheiro de Certificado de Upload,** navegue para a localização do certificado e selecione-o.
1. Em **Password,** insira a chave privada do seu certificado.
1. Clique em **Criar**.

    ![Certificado de importação 1](./media/custom-dns-tutorial/import-certificate-a.png)

Para conceder ao Azure Spring Cloud acesso ao seu cofre-chave antes de importar o certificado:
1. Vá para o seu caso do cofre chave.
1. No painel de navegação à esquerda, clique em **Access Police**.
1. No menu superior, clique em **Adicionar Política de Acesso.**
1. Preencha a informação e clique no botão **Adicionar** e, em seguida, **guarde a** polícia de acesso.

| Permissão secreta | Permissão de certificado | Selecione principal |
|--|--|--|
| Obter, Lista | Obter, Lista | Azure Spring Cloud Domain-Management |

![Certificado de importação 2](./media/custom-dns-tutorial/import-certificate-b.png)

Ou, você pode usar o CLI Azure para conceder ao Azure Spring Cloud acesso ao cofre de chaves.

Obtenha o objeto de identificação através do seguinte comando.
```
az ad sp show --id <service principal id> --query objectId
```

Grant Azure Spring Cloud leia o acesso ao cofre da chave, substitua o id do objeto no seguinte comando.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id <object id> --certificate-permissions get list
``` 

Para importar certificado para Azure Spring Cloud:
1. Vá para o seu caso de serviço. 
1. A partir do painel de navegação esquerdo da sua aplicação, selecione **as definições TLS/SSL**.
1. Em seguida, clique no **Certificado de Cofre de Chave de Importação**.

    ![Importar o certificado](./media/custom-dns-tutorial/import-certificate.png)

Ou, pode usar o CLI Azure para importar o certificado:

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

> [!IMPORTANT] 
> Certifique-se de que concede ao Azure Spring Cloud acesso ao seu cofre de chaves antes de executar o comando anterior do certificado de importação. Se não o fez, pode executar o seguinte comando para conceder os direitos de acesso.

```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list
``` 

Quando tiver importado com sucesso o seu certificado, verá-o na lista de **Certificados-Chave Privados.**

![Certificado chave privado](./media/custom-dns-tutorial/key-certificates.png)

Ou, pode usar o CLI Azure para mostrar uma lista de certificados:

```
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```

> [!IMPORTANT] 
> Para garantir um domínio personalizado com este certificado, ainda precisa de ligar o certificado a um domínio específico. Siga os passos deste documento sob a rubrica **Add SSL Binding**.

## <a name="add-custom-domain"></a>Adicionar domínio personalizado
Você pode usar um registo CNAME para mapear um nome DNS personalizado para Azure Spring Cloud. 

> [!NOTE] 
> O registo A não é suportado. 

### <a name="create-the-cname-record"></a>Criar o registo CNAME
Vá ao seu fornecedor DNS e adicione um registo CNAME para mapear o seu domínio para o <service_name>.azuremicroservices.io. Aqui <service_name> é o nome do seu exemplo de Azure Spring Cloud. Apoiamos o domínio wildcard e o sub domínio. Depois de adicionar o CNAME, a página de registos DNS assemelhar-se-á ao seguinte exemplo: 

![Página de registos DNS](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Mapear o seu domínio personalizado para a aplicação Azure Spring Cloud
Se não tiver uma aplicação em Azure Spring Cloud, siga as instruções em [Quickstart: Lance uma aplicação existente em Azure Spring Cloud utilizando o portal Azure](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master).

Vá para a página de inscrição.

1. Selecione **domínio personalizado**.
2. Em seguida, **adicione domínio personalizado**. 

    ![Domínio personalizado](./media/custom-dns-tutorial/custom-domain.png)

3. Digite o nome de domínio totalmente qualificado para o qual adicionou um registo CNAME, como www.contoso.com. Certifique-se de que o tipo de registo hostname está definido para CNAME (<service_name>.azuremicroservices.io)
4. Clique **em Validar** para ativar o botão **Adicionar.**
5. Clique em **Adicionar**.

    ![Adicionar domínio personalizado](./media/custom-dns-tutorial/add-custom-domain.png)

Ou, pode usar o CLI Azure para adicionar um domínio personalizado:
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

Uma aplicação pode ter vários domínios, mas um domínio só pode mapear para uma aplicação. Quando tiver mapeado com sucesso o seu domínio personalizado para a aplicação, irá vê-lo na tabela de domínio personalizado.

![Tabela de domínio personalizado](./media/custom-dns-tutorial/custom-domain-table.png)

Ou, você pode usar o CLI Azure para mostrar uma lista de domínios personalizados:
```
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```

> [!NOTE]
> Uma etiqueta **Não Segura** para o seu domínio personalizado significa que ainda não está vinculado a um certificado SSL. Qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá um erro ou aviso.

## <a name="add-ssl-binding"></a>Adicionar ligação SSL
Na tabela de domínio personalizado, **selecione Adicionar a ligação ssl** como mostrado na figura anterior.  
1. Selecione o seu **Certificado** ou importe-o.
1. Clique em **Guardar**.

    ![Adicionar encadernação SSL 1](./media/custom-dns-tutorial/add-ssl-binding.png)

Ou, pode utilizar o Azure CLI para **adicionar ligação ssl:**
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> 
```

Depois de adicionar com sucesso a ligação SSL, o estado de domínio estará seguro: **Saudável**. 

![Adicionar encadernação SSL 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Impor HTTPS
Por predefinição, qualquer pessoa ainda pode aceder à sua aplicação utilizando HTTP, mas pode redirecionar todos os pedidos HTTP PARA a porta HTTPS.

Na sua página de aplicações, na navegação à esquerda, selecione **Domínio Personalizado**. Em seguida, definir **HTTPS Apenas,** para *Verdadeiro*.

![Adicionar ligação SSL 3](./media/custom-dns-tutorial/enforce-http.png)

Ou, pode usar o CLI Azure para impor HTTPS:
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```

Quando a operação estiver concluída, navegue para qualquer um dos URLs HTTPS que apontam para a sua aplicação. Note que os URLs HTTP não funcionam.

## <a name="see-also"></a>Consulte também
* [O que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Importar um certificado](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Lance a sua App Cloud Spring utilizando o Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

