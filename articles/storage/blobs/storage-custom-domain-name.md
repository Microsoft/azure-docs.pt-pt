---
title: Configurar um nome de domínio personalizado para a sua conta de armazenamento do Azure | Documentos da Microsoft
description: Utilize o portal do Azure para mapear o seu próprio nome canónico (CNAME) para o ponto final web ou de armazenamento de BLOBs numa conta de armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 7f3b6de64343137278895d92835f080f8844dda1
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588929"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Configurar um nome de domínio personalizado para a sua conta de armazenamento do Azure

Pode configurar um domínio personalizado para aceder a dados de BLOBs na sua conta de armazenamento do Azure. É o ponto final predefinido para o armazenamento de Blobs do Azure  *\<nome da conta de armazenamento >. blob.core.windows.net*. Também pode utilizar o ponto final web que é gerado como parte da [funcionalidade de Web sites estáticos (pré-visualização)](storage-blob-static-website.md). Se mapear um domínio personalizado e o subdomínio, tal como *www.contoso.com*, para o blob ou web ponto final para a sua conta de armazenamento, os utilizadores podem utilizar esse domínio para aceder a dados de BLOBs na sua conta de armazenamento.

> [!IMPORTANT]
> O armazenamento do Azure, mas nativamente suporta HTTPS com domínios personalizados. Pode atualmente [utilizar CDN do Azure para aceder a blobs através da utilização de domínios personalizados através de HTTPS](storage-https-custom-domain-cdn.md).
>

> [!NOTE]  
> Contas de armazenamento atualmente suportam apenas um nome de domínio personalizado por conta. Não é possível mapear um nome de domínio personalizado para pontos finais de serviço da web e de Blobs.

> [!NOTE]  
> O mapeamento só funcionam em subdomínios (por exemplo, www.contoso.com). Se quiser que o seu ponto final da web disponível no domínio raiz (por exemplo, contoso.com), então precisa [utilizar a CDN do Azure com domínios personalizados](storage-https-custom-domain-cdn.md)

A tabela seguinte mostra alguns URLs de exemplo para dados de BLOBs que esteja localizados numa conta de armazenamento com o nome *mystorageaccount*. O subdomínio personalizado que está registado para a conta de armazenamento são *www.contoso.com*:

| Tipo de recurso | URL predefinido | URL de domínio personalizado |
| --- | --- | --- | --- |
| Conta de armazenamento | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Blobs |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Contentor de raiz | http://mystorageaccount.blob.core.windows.net/myblob ou http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob ou http://www.contoso.com/$root/myblob |
| Web |  http://mystorageaccount. [zone].web.core.windows.net/$web/[indexdoc] ou http://mystorageaccount. [ Zone].Web.Core.Windows.NET/[indexdoc] ou http://mystorageaccount. [ Zone].Web.Core.Windows.NET/$Web ou http://mystorageaccount. [ Zone].Web.Core.Windows.NET/ | http://www.contoso.com/$web ou http://www.contoso.com/ ou http://www.contoso.com/$web / [indexdoc] ou http://www.contoso.com/[indexdoc] |

> [!NOTE]  
> Conforme mostrado nas seções a seguir, todos os exemplos para o ponto final de serviço de BLOBs também se aplicam ao ponto de final de serviço da web.

## <a name="direct-vs-intermediary-cname-mapping"></a>Direto versus intermediário mapeamento de CNAME

Pode apontar seu domínio personalizado, o prefixo um subdomínio (por exemplo, www.contoso.com) para o ponto final do blob para a sua conta de armazenamento em qualquer uma das seguintes formas: 
* Utilize o mapeamento direto de CNAME.
* Utilize o *asverifiy* subdomínio intermediário.

### <a name="direct-cname-mapping"></a>Direcionar o mapeamento CNAME

A primeira e mais simples, o método é criar um registo de nome canónico (CNAME) que mapeia o seu domínio personalizado e o subdomínio diretamente para o ponto final do blob. Um registo CNAME é uma funcionalidade (DNS sistema) de nome de domínio que mapeia um domínio de origem para um domínio de destino. No nosso exemplo, o domínio de origem é o seu domínio personalizado e o subdomínio (*www.contoso.com*, por exemplo). O domínio de destino é o ponto final de serviço de BLOBs (*mystorageaccount.blob.core.windows.net*, por exemplo).

O método direto é abordado na seção "Registar um domínio personalizado".

### <a name="intermediary-mapping-with-asverify"></a>Mapeamento de intermediário com *asverifiy*

O segundo método também utiliza registos CNAME. Para evitar o tempo de inatividade, no entanto, primeiro emprega um subdomínio especial *asverifiy* que é reconhecido pelo Azure.

Mapear o seu domínio personalizado para um ponto final do blob pode causar um breve período de tempo de inatividade enquanto estiver a registar o domínio no [portal do Azure](https://portal.azure.com). Se o domínio suporta atualmente uma aplicação com um contrato de nível de serviço (SLA) que precisa de nenhum tempo de inatividade, utilize o Azure *asverifiy* subdomínio como passo intermédio de registo. Este passo garante que os utilizadores podem a aceder ao seu domínio enquanto o mapeamento de DNS está em curso.

O método intermediário é abordado em registar um domínio personalizado utilizando a *asverifiy* subdomínio.

## <a name="register-a-custom-domain"></a>Registe-se um domínio personalizado
Registe o domínio, utilizando o procedimento nesta secção, se aplicam as seguintes instruções:
* Está preocupados-se de que o domínio não está brevemente disponível aos seus utilizadores.
* O domínio personalizado não está atualmente a alojar uma aplicação. 

Pode utilizar o DNS do Azure para configurar um nome DNS personalizado para seu armazenamento de Blobs do Azure. Para obter mais informações, veja [Utilizar o DNS do Azure para oferecer definições de domínio personalizado para um serviço do Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Se o seu domínio personalizado, atualmente, suporta um aplicativo que não pode ter qualquer período de inatividade, utilize o procedimento no Registre-se um domínio personalizado, utilizando o *asverifiy* subdomínio.

Para configurar um nome de domínio personalizado, crie um novo registo CNAME no DNS. O registo CNAME Especifica um alias para um nome de domínio. No nosso exemplo, ele mapeia o endereço do seu domínio personalizado ao ponto final de armazenamento de BLOBs da conta de armazenamento.

Normalmente, pode gerir as definições de DNS do seu domínio no site da entidade de domínio. Cada entidade de registo tem um método semelhante, mas ligeiramente diferente de especificar um registo CNAME, mas o conceito é o mesmo. Uma vez que alguns pacotes de registo de domínio básico não oferecem a configuração de DNS, poderá ter de atualizar o seu pacote de registo do domínio antes de poder criar o registo CNAME.

1. Na [portal do Azure](https://portal.azure.com), aceda à sua conta de armazenamento.

1. No painel de menu, sob **serviço Blob**, selecione **Custom domain**.  
   O **Custom domain** painel abre-se.

1. Inicie sessão no site da entidade de domínio e, em seguida, vá para a página para a gestão de DNS.  
   Pode encontrar a página numa seção chamada **nome de domínio**, **DNS**, ou **nome do servidor de gestão**.

1. Encontre a secção para gerir CNAMEs.  
   Poderá ter de ir para uma página de definições avançadas e procure **CNAME**, **Alias**, ou **subdomínios**.

1. Criar um novo registo CNAME, introduza um alias de subdomínio, tal como **www** ou **fotos** (é necessário o subdomínio, domínios de raiz não são suportados) e, em seguida, forneça um nome de anfitrião.  
   O nome de anfitrião é o ponto final de serviço de Blobs. Seu formato é  *\<mystorageaccount >. blob.core.windows.net*, em que *mystorageaccount* é o nome da conta de armazenamento. O nome de anfitrião a utilizar é apresentado no item #1 do **Custom domain** painel na [portal do Azure](https://portal.azure.com). 

1. Na **Custom domain** painel, na caixa de texto, introduza o nome do seu domínio personalizado, incluindo o subdomínio.  
   Por exemplo, se o seu domínio for *contoso.com* e é o seu alias de subdomínio *www*, introduza **www.contoso.com**. Se for o subdomínio *fotografias*, introduza **photos.contoso.com**.

1. Para registar o seu domínio personalizado, selecione **guardar**.  
   Se o registo for bem sucedido, o portal notifica-o que sua conta de armazenamento foi atualizada com êxito.

Depois de ter sido propagada seu novo registo CNAME através de DNS, se os utilizadores têm as permissões adequadas, podem visualizar os dados de Blobs com o seu domínio personalizado.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>Registe-se de um domínio personalizado, utilizando o *asverifiy* subdomínio
Se o seu domínio personalizado, atualmente, suporta um aplicativo com um SLA que não requer que haja nenhum tempo de inatividade, registe o seu domínio personalizado ao utilizar o procedimento nesta secção. Ao criar um CNAME que aponte do *asverifiy.\< subdomínio >. \<customdomain >* ao *asverifiy.\< storageaccount >. blob.core.windows.net*, pode registrar previamente seu domínio com o Azure. Em seguida, pode criar um segundo CNAME que aponte do  *\<subdomínio >.\< customdomain >* para  *\<storageaccount >. blob.core.windows.net*, e, em seguida, o tráfego ao seu domínio personalizado é direcionado para o ponto final blob.

O *asverifiy* subdomínio é um subdomínio especial reconhecido pelo Azure. Por prefixação *asverifiy* a sua própria subdomínio, permitir que o Azure reconheça seu domínio personalizado sem ter de modificar o registo DNS para o domínio. Ao modificar o registo DNS para o domínio, ele será mapeado para o ponto final do blob sem períodos de indisponibilidade.

1. Na [portal do Azure](https://portal.azure.com), aceda à sua conta de armazenamento.

1. No painel de menu, sob **serviço Blob**, selecione **Custom domain**.  
   O **Custom domain** painel abre-se.

1. Inicie sessão no Web site do seu fornecedor de DNS e, em seguida, vá para a página para a gestão de DNS.  
   Pode encontrar a página numa seção chamada **nome de domínio**, **DNS**, ou **nome do servidor de gestão**.

1. Encontre a secção para gerir CNAMEs.  
   Poderá ter de ir para uma página de definições avançadas e procure **CNAME**, **Alias**, ou **subdomínios**.

1. Criar um novo registo CNAME, forneça um alias de subdomínio que inclui a *asverifiy* subdomínio, tal como **asverify.www** ou **asverify.photos**e, em seguida, forneça um nome de anfitrião.  
   O nome de anfitrião é o ponto final de serviço de Blobs. Seu formato é *asverifiy.\< mystorageaccount >. blob.core.windows.net*, onde *mystorageaccount* é o nome da conta de armazenamento. O nome de anfitrião a utilizar é apresentado no item #2 do *Custom domain* painel na [portal do Azure](https://portal.azure.com).

1. Na **Custom domain** painel, na caixa de texto, introduza o nome do seu domínio personalizado, incluindo o subdomínio.  
   Não inclua *asverifiy*. Por exemplo, se o seu domínio for *contoso.com* e é o seu alias de subdomínio *www*, introduza **www.contoso.com**. Se for o subdomínio *fotografias*, introduza **photos.contoso.com**.

1. Selecione o **utilizam a validação de CNAME indireta** caixa de verificação.

1. Para registar o seu domínio personalizado, selecione **guardar**.  
   Se o registo for bem sucedido, o portal notifica-o que sua conta de armazenamento foi atualizada com êxito. O domínio personalizado foi verificado pelo Azure, mas o tráfego ao seu domínio ainda não está a ser encaminhado para sua conta de armazenamento.

1. Voltar ao site do seu fornecedor de DNS e, em seguida, crie outro registo CNAME que mapeia o subdomínio para o ponto final de serviço de Blobs.  
   Por exemplo, especifique o subdomínio como *www* ou *fotos* (sem o *asverifiy*) e especifique o nome do anfitrião como  *\<mystorageaccount >. blob.core.windows.net*, onde *mystorageaccount* é o nome da conta de armazenamento. Neste passo, o registo do seu domínio personalizado está concluído.

1. Por fim, pode eliminar o registo CNAME recém-criado, que contém o *asverifiy* subdomínio, que era necessário apenas como uma etapa intermediária.

Depois de ter sido propagada seu novo registo CNAME através de DNS, se os utilizadores têm as permissões adequadas, podem visualizar os dados de Blobs com o seu domínio personalizado.

## <a name="test-your-custom-domain"></a>Testar o seu domínio personalizado

Para confirmar que o seu domínio personalizado está mapeado para o ponto de final de serviço blob, crie um blob num contentor público na sua conta de armazenamento. Em seguida, num navegador da web, aceda ao blob utilizando um URI no seguinte formato: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Por exemplo, para aceder a um formulário da web na *myforms* contentor no *photos.contoso.com* subdomínio personalizado, poderá usar o URI seguinte: `http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Anular o registo um domínio personalizado

Para anular o registo um domínio personalizado para o ponto final de armazenamento de BLOBs, utilize um dos seguintes procedimentos.

### <a name="azure-portal"></a>Portal do Azure

Para remover a definição de domínio personalizado, faça o seguinte:

1. Na [portal do Azure](https://portal.azure.com), aceda à sua conta de armazenamento.

1. No painel de menu, sob **serviço Blob**, selecione **Custom domain**.  
   O **Custom domain** painel abre-se.

1. Limpe o conteúdo da caixa de texto que contém o nome de domínio personalizado.

1. Selecione o botão **Guardar**.

Depois do domínio personalizado foi removido com êxito, verá uma notificação do portal que sua conta de armazenamento foi atualizada com êxito.

### <a name="azure-cli"></a>CLI do Azure

Para remover um registo de domínio personalizado, utilize o [atualização da conta de armazenamento az](https://docs.microsoft.com/cli/azure/storage/account) CLI de comandos e, em seguida, especifique uma cadeia vazia (`""`) para o `--custom-domain` valor do argumento.

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

Para remover um registo de domínio personalizado, utilize o [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) cmdlet do PowerShell e, em seguida, especifique uma cadeia vazia (`""`) para o `-CustomDomainName` valor do argumento.

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

## <a name="next-steps"></a>Passos Seguintes
* [Mapear um domínio personalizado para um ponto de extremidade de rede de entrega conteúdo (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Utilizar a CDN do Azure para aceder a blobs através da utilização de domínios personalizados através de HTTPS](storage-https-custom-domain-cdn.md)
* [Alojar o Web site estático no armazenamento de Blobs do Azure (pré-visualização)](storage-blob-static-website.md)
