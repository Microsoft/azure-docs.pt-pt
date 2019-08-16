---
title: Configurar um nome de domínio personalizado para sua conta de armazenamento do Azure | Microsoft Docs
description: Use o portal do Azure para mapear seu próprio nome canônico (CNAME) para o armazenamento de BLOBs ou ponto de extremidade da Web em uma conta de armazenamento do Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 2359befc05bff867a8f8b17943ed67d906ff4971
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534335"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Configurar um nome de domínio personalizado para sua conta de armazenamento do Azure

Você pode configurar um domínio personalizado para acessar dados de BLOB em sua conta de armazenamento do Azure. O ponto de extremidade padrão para o armazenamento de BLOBs do Azure é  *\<o nome da conta de armazenamento >. blob. Core. Windows. net*. Você também pode usar o ponto de extremidade da Web que é gerado como parte do [recurso de sites estáticos](storage-blob-static-website.md). Se você mapear um domínio e subdomínio personalizados, como *www\.contoso.com*, para o BLOB ou ponto de extremidade da Web para sua conta de armazenamento, os usuários poderão usar esse domínio para acessar dados de BLOB em sua conta de armazenamento.

> [!IMPORTANT]
> O armazenamento do Azure ainda não dá suporte nativo a HTTPS com domínios personalizados. No momento, você pode [usar a CDN do Azure para acessar BLOBs usando domínios personalizados por HTTPS](storage-https-custom-domain-cdn.md).
> 
> 
> [!NOTE]
> Atualmente, as contas de armazenamento dão suporte apenas a um nome de domínio personalizado por conta. Não é possível mapear um nome de domínio personalizado para os pontos de extremidade de serviço Web e BLOB.
> 
> [!NOTE]
> O mapeamento só funciona para subdomínios (por exemplo, www\.contoso.com). Se você quiser ter o ponto de extremidade da Web disponível no domínio raiz (por exemplo, contoso.com), precisará [Adicionar um domínio personalizado ao ponto de extremidade da CDN do Azure](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain).

A tabela a seguir mostra algumas URLs de exemplo para dados de BLOB que estão localizados em uma conta de armazenamento denominada *mystorageaccount*. O subdomínio personalizado registrado para a conta de armazenamento é *contoso.com da\.Web*:

| Tipo de recurso | URL predefinido | URL de domínio personalizado |
| --- | --- | --- |
| Conta de armazenamento | http:\//mystorageaccount.blob.core.windows.net | http:\//www.contoso.com |
| Blob |http:\//mystorageaccount.blob.core.windows.net/mycontainer/myblob | http:\//www.contoso.com/MyContainer/myblob |
| Contêiner raiz | http:\//mystorageaccount.blob.Core.Windows.net/myblob ou http:\//mystorageaccount.blob.Core.Windows.net/$root/myblob | http:\//www.contoso.com/myblob ou http:\//www.contoso.com/$root/myblob |
| Web |  http:\//mystorageaccount. [ Zone]. Web. Core. Windows. NET/$Web/[indexdoc] ou http:\//mystorageaccount. [ Zone]. Web. Core. Windows. net/[indexdoc] ou http:\//mystorageaccount. [ Zone]. Web. Core. Windows. NET/$Web ou http:\//mystorageaccount. [ Zone]. Web. Core. Windows. net/ | http:\//www.contoso.com/$Web ou http:\//www.contoso.com/ou http:\//www.contoso.com/$Web/[indexdoc] ou http:\//www.contoso.com/[indexdoc] |

> [!NOTE]  
> Conforme mostrado nas seções a seguir, todos os exemplos para o ponto de extremidade do serviço blob também se aplicam ao ponto de extremidade do serviço Web.

## <a name="direct-vs-intermediary-cname-mapping"></a>Mapeamento de CNAME direto vs. intermediário

Você pode apontar seu domínio personalizado prefixado com um subdomínio (por exemplo\., www contoso.com) para o ponto de extremidade de BLOB para sua conta de armazenamento de uma das duas maneiras: 
* Use mapeamento de CNAME direto.
* Use o subdomínio intermediário asverify.

### <a name="direct-cname-mapping"></a>Mapeamento de CNAME direto

O primeiro método, e mais simples, é criar um registro de nome canônico (CNAME) que mapeie seu domínio e subdomínio personalizados diretamente para o ponto de extremidade do blob. Um registro CNAME é um recurso de DNS (sistema de nomes de domínio) que mapeia um domínio de origem para um domínio de destino. Em nosso exemplo, o domínio de origem é seu próprio domínio e subdomínio personalizados *(\.www contoso.com*, por exemplo). O domínio de destino é o ponto de extremidade do serviço BLOB (*mystorageaccount.blob.Core.Windows.net*, por exemplo).

O método direto é abordado na seção "registrar um domínio personalizado".

### <a name="intermediary-mapping-with-asverify"></a>Mapeamento intermediário com asverify

O segundo método também usa registros CNAME. No entanto, para evitar o tempo de inatividade, ele primeiro emprega um subdomínio especial que é reconhecido pelo Azure.

O mapeamento de seu domínio personalizado para um ponto de extremidade de blob pode causar um breve período de inatividade enquanto você estiver registrando o domínio no [portal do Azure](https://portal.azure.com). Se o domínio atualmente dá suporte a um aplicativo com um SLA (contrato de nível de serviço) que exige zero tempo de inatividade, use o subdomínio Asverify do Azure como uma etapa de registro intermediário. Essa etapa garante que os usuários possam acessar seu domínio enquanto o mapeamento de DNS ocorre.

O método intermediário é abordado no registro de um domínio personalizado usando o subdomínio *asverify* .

## <a name="register-a-custom-domain"></a>Registrar um domínio personalizado
Registre o domínio usando o procedimento nesta seção se as seguintes instruções se aplicarem:
* Você não tem interesse em que o domínio esteja rapidamente indisponível para os usuários.
* Seu domínio personalizado não está hospedando um aplicativo no momento. 

Você pode usar o DNS do Azure para configurar um nome DNS personalizado para o repositório de blob do Azure. Para obter mais informações, veja [Utilizar o DNS do Azure para oferecer definições de domínio personalizado para um serviço do Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Se o seu domínio personalizado atualmente dá suporte a um aplicativo que não pode ter nenhum tempo de inatividade, use o procedimento em registrar um domínio personalizado usando o subdomínio asverify.

Para configurar um nome de domínio personalizado, crie um novo registro CNAME no DNS. O registro CNAME especifica um alias para um nome de domínio. Em nosso exemplo, ele mapeia o endereço do seu domínio personalizado para o ponto de extremidade de armazenamento de BLOB da sua conta de armazenamento.

Normalmente, você pode gerenciar as configurações de DNS do seu domínio no site do registrador de domínio. Cada registrador tem um método semelhante, mas ligeiramente diferente, de especificar um registro CNAME, mas o conceito é o mesmo. Como alguns pacotes de registro de domínio básicos não oferecem configuração de DNS, talvez seja necessário atualizar seu pacote de registro de domínio antes de criar o registro CNAME.

1. Na [portal do Azure](https://portal.azure.com), vá para sua conta de armazenamento.

1. No painel de menus, em **serviço blob**, selecione **domínio personalizado**.  
   O painel **domínio personalizado** é aberto.

1. Entre no site do registrador de domínio e vá para a página de gerenciamento de DNS.  
   Você pode encontrar a página em uma seção chamada **nome de domínio**, **DNS**ou **Gerenciamento de servidor de nome**.

1. Encontre a secção para gerir CNAMEs.  
   Talvez seja necessário ir para uma página de configurações avançadas e procurar **CNAME**, **alias**ou subdomínios.

1. Crie um novo registro CNAME, insira um alias de subdomínio, como **www** ou **photos** (subdomínio é necessário, não há suporte para domínios raiz) e forneça um nome de host.  
   O nome do host é o ponto de extremidade do serviço BLOB. Seu formato é  *\<mystorageaccount >. blob. Core. Windows. net*, em que *mystorageaccount* é o nome da sua conta de armazenamento. O nome do host a ser usado aparece no item #1 do painel **domínio personalizado** na [portal do Azure](https://portal.azure.com). 

1. No painel **domínio personalizado** , na caixa de texto, digite o nome do seu domínio personalizado, incluindo o subdomínio.  
   Por exemplo, se seu domínio for *contoso.com* e o alias de subdomínio for *www*, **digite\.www contoso.com**. Se o subdomínio for *photos*, insira **photos.contoso.com**.

1. Para registrar seu domínio personalizado, selecione **salvar**.  
   Se o registro for bem-sucedido, o portal notificará que sua conta de armazenamento foi atualizada com êxito.

Depois que o novo registro CNAME for propagado por meio do DNS, se os usuários tiverem as permissões apropriadas, eles poderão exibir dados de BLOB usando seu domínio personalizado.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>Registrar um domínio personalizado usando o subdomínio *asverify*
Se o seu domínio personalizado atualmente dá suporte a um aplicativo com um SLA que exige que não haja nenhum tempo de inatividade, Registre seu domínio personalizado usando o procedimento nesta seção. Criando um CNAME que aponta de *asverify.\< > de subdomínio. customdomain\<>* para *asverify.\< storageAccount >. blob. Core. Windows. net*, você pode registrar previamente seu domínio no Azure. Em seguida, você pode criar um segundo CNAME que aponta do  *\<subdomínio\< >. customdomain*  *>\<storageAccount >. blob. Core. Windows. net*e o tráfego para seu domínio personalizado é direcionado para o ponto de extremidade do blob.

O subdomínio asverify é um subdomínio especial reconhecido pelo Azure. Ao fazer uma *verificação* de acordo com o seu próprio subdomínio, você permite que o Azure reconheça seu domínio personalizado sem precisar modificar o registro DNS do domínio. Quando você modificar o registro DNS para o domínio, ele será mapeado para o ponto de extremidade de blob sem tempo de inatividade.

1. Na [portal do Azure](https://portal.azure.com), vá para sua conta de armazenamento.

1. No painel de menus, em **serviço blob**, selecione **domínio personalizado**.  
   O painel **domínio personalizado** é aberto.

1. Entre no site do provedor de DNS e vá para a página de gerenciamento de DNS.  
   Você pode encontrar a página em uma seção chamada **nome de domínio**, **DNS**ou **Gerenciamento de servidor de nome**.

1. Encontre a secção para gerir CNAMEs.  
   Talvez seja necessário ir para uma página de configurações avançadas e procurar **CNAME**, **alias**ou subdomínios.

1. Crie um novo registro CNAME, forneça um alias de subdomínio que inclua o subdomínio asverify, como **asverify. www** ou **asverify. photos**, e forneça um nome de host.  
   O nome do host é o ponto de extremidade do serviço BLOB. Seu formato é *asverify.\< mystorageaccount >. blob. Core. Windows. net*, em que *mystorageaccount* é o nome da sua conta de armazenamento. O nome do host a ser usado aparece no item #2 do painel *domínio personalizado* na [portal do Azure](https://portal.azure.com).

1. No painel **domínio personalizado** , na caixa de texto, digite o nome do seu domínio personalizado, incluindo o subdomínio.  
   Não inclua *asverify*. Por exemplo, se seu domínio for *contoso.com* e o alias de subdomínio for *www*, **digite\.www contoso.com**. Se o subdomínio for *photos*, insira **photos.contoso.com**.

1. Marque a caixa de seleção **usar validação de CNAME indireta** .

1. Para registrar seu domínio personalizado, selecione **salvar**.  
   Se o registro for bem-sucedido, o portal notificará que sua conta de armazenamento foi atualizada com êxito. Seu domínio personalizado foi verificado pelo Azure, mas o tráfego para seu domínio ainda não está sendo roteado para sua conta de armazenamento.

1. Retorne ao site do provedor de DNS e crie outro registro CNAME que mapeie seu subdomínio para o ponto de extremidade do serviço BLOB.  
   Por exemplo, especifique o subdomínio como *www* ou *photos* (sem *asverify*) e especifique o nome do host como  *\<mystorageaccount >. blob. Core. Windows. net*, em que *mystorageaccount* é o nome do seu conta de armazenamento. Com essa etapa, o registro do seu domínio personalizado é concluído.

1. Por fim, você pode excluir o registro CNAME recém-criado que contém o subdomínio asverify, que era necessário apenas como uma etapa intermediária.

Depois que o novo registro CNAME for propagado por meio do DNS, se os usuários tiverem as permissões apropriadas, eles poderão exibir dados de BLOB usando seu domínio personalizado.

## <a name="test-your-custom-domain"></a>Testar seu domínio personalizado

Para confirmar que seu domínio personalizado está mapeado para o ponto de extremidade do serviço BLOB, crie um blob em um contêiner público dentro de sua conta de armazenamento. Em seguida, em um navegador da Web, acesse o blob usando um URI no seguinte formato:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Por exemplo, para acessar um formulário da Web no contêiner MyForms no subdomínio personalizado do *photos.contoso.com* , você pode usar o seguinte URI:`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Cancelar o registro de um domínio personalizado

Para cancelar o registro de um domínio personalizado para o ponto de extremidade do armazenamento de BLOBs, use um dos procedimentos a seguir.

### <a name="azure-portal"></a>Portal do Azure

Para remover a configuração de domínio personalizado, faça o seguinte:

1. Na [portal do Azure](https://portal.azure.com), vá para sua conta de armazenamento.

1. No painel de menus, em **serviço blob**, selecione **domínio personalizado**.  
   O painel **domínio personalizado** é aberto.

1. Limpe o conteúdo da caixa de texto que contém seu nome de domínio personalizado.

1. Selecione o botão **Guardar**.

Depois que o domínio personalizado tiver sido removido com êxito, você verá uma notificação do portal de que sua conta de armazenamento foi atualizada com êxito.

### <a name="azure-cli"></a>CLI do Azure

Para remover um registro de domínio personalizado, use o comando [AZ Storage Account Update](https://docs.microsoft.com/cli/azure/storage/account) CLI e especifique uma cadeia de caracteres vazia`""`() para `--custom-domain` o valor do argumento.

* Formato do comando:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Exemplo de comando:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para remover um registro de domínio personalizado, use o cmdlet do PowerShell [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) e especifique uma cadeia de caracteres`""`vazia () `-CustomDomainName` para o valor do argumento.

* Formato do comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Exemplo de comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Passos seguintes
* [Mapear um domínio personalizado para um ponto de extremidade da CDN (rede de distribuição de conteúdo) do Azure](../../cdn/cdn-map-content-to-custom-domain.md)
* [Usar a CDN do Azure para acessar BLOBs usando domínios personalizados por HTTPS](storage-https-custom-domain-cdn.md)
* [Hospedagem de site estático no armazenamento de BLOBs do Azure (visualização)](storage-blob-static-website.md)
