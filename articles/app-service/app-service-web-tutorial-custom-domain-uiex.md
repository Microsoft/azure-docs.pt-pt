---
title: 'Tutorial: Mapeie o nome DNS personalizado existente'
description: Saiba como adicionar um nome de domínio dNS personalizado existente (domínio de vaidade) a uma aplicação web, aplicativo móvel ou app API no Azure App Service.
keywords: serviço de aplicativos, serviço de aplicativos azure, mapeamento de domínio, nome de domínio, domínio existente, nome anfitrião, domínio da vaidade
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 476a88e41382842d91859d319a571784bd6e9b49
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748199"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Tutorial: Mapeie um nome DNS personalizado existente para o Azure App Service

Este tutorial mostra-lhe como mapear qualquer existente <abbr title="Um nome de domínio que adquiriu a um registrador de domínio, como o GoDaddy, ou um subdomínio do seu domínio adquirido.">nome de domínio dNS personalizado</abbr> para <abbr title="Um serviço baseado em HTTP para hospedar aplicações web, APIs REST e aplicações móveis de back-end.">Serviço de Aplicações do Azure</abbr>.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Mapear um subdomínio usando um <abbr title="Um registo de nomes canónicos DNS mapeia um nome de domínio para outro.">Registo CNAME</abbr>.
> * Mapear um domínio de raiz usando um <abbr title="Um registo de endereço no DNS mapeia um nome de anfitrião para um endereço IP.">Um registo</abbr>.
> * Mapear um domínio wildcard usando um registo CNAME.
> * Redirecione o URL padrão para um diretório personalizado.

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

* [Crie uma aplicação do Serviço de Aplicações](./index.yml) ou utilize uma aplicação que tenha criado para outro tutorial.
* Certifique-se de que pode editar registos DNS para o seu domínio personalizado. Se ainda não tiver um domínio personalizado, pode [adquirir um domínio de Serviço de Aplicações.](manage-custom-dns-buy-domain.md)

    <details>
        <summary>O que preciso para editar registos DNS?</summary>
        Requer acesso ao registo DNS para o seu fornecedor de domínio, como o GoDaddy. Por exemplo, para adicionar entradas de DNS para contoso.com e www.contoso.com, deve ser capaz de configurar as definições de DNS para o domínio raiz contoso.com.
    </details>

## <a name="2-prepare-the-app"></a>2. Preparar a aplicação

Para mapear um nome DNS personalizado para uma aplicação, a aplicação <abbr title="Especifica a localização, o tamanho e as funcionalidades da fazenda do servidor web que hospeda a sua aplicação.">Plano do Serviço de Aplicações</abbr> deve ser um nível pago (não <abbr title="Um nível de Serviço de Aplicações Azure em que a sua aplicação funciona nos mesmos VMs que outras aplicações, incluindo aplicações de outros clientes. Este nível destina-se ao desenvolvimento e ao teste.">**Grátis (F1)**</abbr>). Para mais informações, consulte [a visão geral do plano do Azure App Service](overview-hosting-plans.md).

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra o [portal Azure](https://portal.azure.com)e inscreva-se na sua conta Azure.

### <a name="select-the-app-in-the-azure-portal"></a>Selecione a aplicação no portal Azure

1. Procure e selecione **Serviços de Aplicações.**

   ![Screenshot que mostra a seleção de Serviços de Aplicações.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Na página **Serviços de Aplicações,** selecione o nome da sua aplicação Azure.

   ![Screenshot mostrando navegação de portal para uma aplicação Azure.](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Será apresentada a página de gestão da aplicação do Serviço de Aplicações.

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Verificar o escalão de preço

1. No painel esquerdo da página da aplicação, percorra a secção **Definições** e selecione **Scale up (Plano de Serviço de Aplicações)**.

   ![Screenshot que mostra o menu Scale up (App Service plan).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. O escalão atual da aplicação é realçado com um limite azul. Verifique se a aplicação não está no nível **de F1.** O DNS personalizado não é suportado no nível **de F1.**

   ![Screenshot que mostra os níveis de preços recomendados.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Se o plano de Serviço de Aplicações não estiver no nível **F1,** feche a página **Scale up** e salte para [3. Obtenha uma identificação de verificação de domínio.](#3-get-a-domain-verification-id)

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Aumentar verticalmente o plano do Serviço de Aplicações

1. Selecione qualquer uma das camadas não gratuitas (**D1**, **B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para opções adicionais, **selecione Ver opções adicionais**.

1. Selecione **Aplicar**.

   ![Screenshot que mostra a verificação do nível de preços.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Quando vir a notificação seguinte, significa que a operação de dimensionamento está completa.

   ![Screenshot que mostra a confirmação da operação de escala.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3. Obter um ID de verificação de domínio

Para adicionar um domínio personalizado à sua aplicação, tem de verificar a sua propriedade do domínio adicionando um ID de verificação como registo TXT com o seu fornecedor de domínio. 

1. No painel esquerdo da sua página de aplicações, selecione **domínios personalizados**. 
1. Copie o ID na caixa de identificação de **verificação de domínio personalizado** na página de **Domínios Personalizados** para o passo seguinte.

    ![Screenshot que mostra o ID na caixa de identificação de verificação de domínio personalizado.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>Por que preciso disto?</summary>
        Adicionar IDs de verificação de domínio ao seu domínio personalizado pode impedir entradas de DNS pendentes e ajudar a evitar aquisições de subdomínios. Para domínios personalizados que previamente configurado sem este ID de verificação, deverá protegê-los do mesmo risco adicionando o ID de verificação ao seu registo DNS. Para obter mais informações sobre esta ameaça comum de alta gravidade, consulte [a aquisição de Subdomínio.](../security/fundamentals/subdomain-takeover.md)
    </details>
    
<a name="info"></a>

3. **(Apenas um recorde) ** Para mapear um <abbr title="Um registo de endereço no DNS mapeia um nome de anfitrião para um endereço IP.">Um registo</abbr>, você precisa do endereço IP externo da aplicação. Na página **de domínios personalizados,** copie o valor do **endereço IP**.

   ![Screenshot que mostra a navegação do portal para uma aplicação Azure.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

## <a name="4-create-the-dns-records"></a>4. Criar os registos DNS

1. Inicie sessão no site do fornecedor do seu domínio.

    <details>
        <summary>Posso gerir o DNS do meu fornecedor de domínio usando o Azure?</summary>
        Se quiser, pode utilizar o Azure DNS para gerir os registos DNS para o seu domínio e configurar um nome DNS personalizado para o Azure App Service. Para mais informações, consulte <a href="https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns">Tutorial: Hospedar o seu domínio em Azure DNS></a>.
    </details>

1. Localize a página para gerir os registos DNS. 

    <details>
        <summary>Como encontro a página?</summary>
        <p>Cada fornecedor de domínio tem a sua própria interface de registos DNS, por isso, consulte a documentação do fornecedor. Procure áreas do site com os nomes <strong>Nome de Domínio</strong>, <strong>DNS</strong> ou <strong>Gestão de Servidor de Nomes</strong>.</p>
        <p>Frequentemente, pode encontrar a página de registos DNS visualizando as informações da sua conta e, em seguida, procurando um link como <strong>os meus domínios</strong>. Vá a essa página e procure um link que tenha o nome de algo como <strong>Zone file</strong>, <strong>DNS Records</strong>ou <strong>configuração Avançada</strong>.</p>
    </details>

   A captura de ecrã seguinte mostra um exemplo de uma página de registos DNS:

   ![Screenshot que mostra uma página de registos DNS exemplo.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Selecione **Adicionar** ou o widget apropriado para criar um registo. 

1. Selecione o tipo de registo para criar e siga as instruções. Você pode usar qualquer um <abbr title="Um registo de nome canónico em DNS mapeia um nome de domínio (um pseudónimo) para outro (o nome canónico).">Registo CNAME</abbr> ou um <abbr title="Um registo de endereço no DNS mapeia um nome de anfitrião para um endereço IP.">Um registo</abbr> para mapear um nome DNS personalizado para o Serviço de Aplicações. 

    <details>
        <summary>Que disco devo escolher?</summary>
        <div>
            <ul>
            <li>Para mapear o domínio raiz (por exemplo, <code>contoso.com</code> ), utilize um registo A. Não utilize o registo CNAME para o registo de raiz (para obter informações, consulte a entrada na <a href="https://en.wikipedia.org/wiki/CNAME_record">Wikipédia).</a></li>
            <li>Para mapear um subdomínio (por exemplo, <code>www.contoso.com</code> ), utilize um registo CNAME.</li>
            <li>Pode mapear um subdomínio para o endereço IP da aplicação diretamente com um registo A, mas é possível que <a href="https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">o endereço IP seja alterado</a>. Em vez disso, o CNAME mapeia o nome de anfitrião da aplicação, o que é menos suscetível a mudanças.</li>
            <li>Para mapear um <a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">domínio wildcard</a> (por exemplo, <code>*.contoso.com</code> ), utilize um registo CNAME.</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

Para um subdomínio como `www` em , crie dois `www.contoso.com` registos de acordo com a tabela seguinte:

    | Tipo de registo | Anfitrião | Valor | Comentários |
    | - | - | - |
    | CNAME | `<subdomain>` (por exemplo, `www` ) | `<app-name>.azurewebsites.net` | O próprio mapeamento de domínio. |
    | TXT | `asuid.<subdomain>` (por exemplo, `asuid.www` ) | [A verificação que recebeu mais cedo](#3-get-a-domain-verification-id) | O Serviço de Aplicações acede ao `asuid.<subdomain>` registo TXT para verificar a sua propriedade do domínio personalizado. |
    
    ![Screenshot that shows the portal navigation to an Azure app.](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

Para um domínio de raiz como `contoso.com` , crie dois registos de acordo com a tabela seguinte:

    | Tipo de registo | Anfitrião | Valor | Comentários |
    | - | - | - |
    | A | `@` | Endereço IP de [Copiar o endereço IP da aplicação](#3-get-a-domain-verification-id) | O próprio mapeamento de domínio `@` (normalmente representa o domínio raiz). |
    | TXT | `asuid` | [A verificação que recebeu mais cedo](#3-get-a-domain-verification-id) | O Serviço de Aplicações acede ao `asuid.<subdomain>` registo TXT para verificar a sua propriedade do domínio personalizado. Para o domínio raiz, utilize `asuid` . |
    
    ![Screenshot that shows a DNS records page.](./media/app-service-web-tutorial-custom-domain/a-record.png)

    <details>
    <summary>What if I want to map a subdomain with an A record?</summary>
    To map a subdomain like `www.contoso.com` with an A record instead of a recommended CNAME record, your A record and TXT record should look like the following table instead:

    | Tipo de registo | Anfitrião | Valor |
    | - | - | - |
    | A | `<subdomain>` (por exemplo, `www` ) | Endereço IP de [Copiar o endereço IP da aplicação](#info) |
    | TXT | `asuid.<subdomain>` (por exemplo, `asuid.www` ) | [A verificação que recebeu mais cedo](#3-get-a-domain-verification-id) |
    </details>
    
# <a name="wildcard-cname"></a>[Wildcard (CNAME)](#tab/wildcard)

Para um nome wildcard como `*` em , crie dois `*.contoso.com` registos de acordo com a tabela seguinte:

    | Tipo de registo | Anfitrião | Valor | Comentários |
    | - | - | - |
    | CNAME | `*` | `<app-name>.azurewebsites.net` | O próprio mapeamento de domínio. |
    | TXT | `asuid` | [A verificação que recebeu mais cedo](#3-get-a-domain-verification-id) | O Serviço de Aplicações acede ao `asuid` registo TXT para verificar a sua propriedade do domínio personalizado. |
    
    ![Screenshot that shows the navigation to an Azure app.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
---

    <details>
        <summary>My changes are erased after I leave the page.</summary>
        For certain providers, such as GoDaddy, changes to DNS records don't become effective until you select a separate **Save Changes** link.
    </details>
    
## <a name="5-enable-the-mapping-in-your-app"></a>5. Ativar o mapeamento na sua aplicação

1. No painel esquerdo da página da aplicação no portal Azure, selecione **domínios personalizados**.

    ![Screenshot que mostra o menu de domínios personalizados.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **Selecione Adicionar domínio personalizado**.

    ![Screenshot que mostra o item do nome do anfitrião Adicionar.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. Escreva o nome de domínio completamente qualificado ao qual adicionou um registo CNAME, como `www.contoso.com`.

1. Selecione **Validar**. Aparece a página **de domínio de adicionar personalizado.**

1. Certifique-se de que **o tipo de registo hostname** está definido para **CNAME (www \. example.com ou qualquer subdomínio)**. **Selecione Adicionar domínio personalizado**.

    ![Screenshot que mostra o botão de domínio personalizado Adicionar.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **de Domínios Personalizados** da aplicação. Atualize o navegador para atualizar os dados.

    ![Screenshot que mostra a adição do disco CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary>O que se passa com o rótulo de aviso <strong>"Não Seguro"?</strong></summary>
        Um rótulo de aviso para o seu domínio personalizado significa que ainda não está ligado a um certificado TLS/SSL. Qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá um erro ou aviso, dependendo do navegador. Para adicionar uma ligação TLS, consulte <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">Secure um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service</a>.
    </details>

    Se perdeu um passo ou fez um erro de erro algures mais cedo, um erro de verificação aparece na parte inferior da página.

    ![Screenshot que mostra um erro de verificação.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. Escreva o nome de domínio completamente qualificado para o qual configurou o registo A, como `contoso.com`. 

1. Selecione **Validar**. É mostrada a página **de domínio do Add custom.**

1. Confirme que o **Tipo de registo de nome de anfitrião** está definido como **Registo A (www.example.com)**. **Selecione Adicionar domínio personalizado**.

    ![Screenshot que mostra adicionar um nome DNS à aplicação.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **de Domínios Personalizados** da aplicação. Atualize o navegador para atualizar os dados.

    ![Screenshot que mostra a adição de um disco A.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary>O que se passa com o rótulo de aviso <strong>"Não Seguro"?</strong></summary>
        Um rótulo de aviso para o seu domínio personalizado significa que ainda não está ligado a um certificado TLS/SSL. Qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá um erro ou aviso, dependendo do navegador. Para adicionar uma ligação TLS, consulte <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">Secure um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service</a>.
    </details>
    
    Se perdeu um passo ou fez um erro de erro algures mais cedo, um erro de verificação aparece na parte inferior da página.
    
    ![Screenshot mostrando um erro de verificação.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[Wildcard (CNAME)](#tab/wildcard)

3. Digite um nome de domínio totalmente qualificado que corresponda ao domínio wildcard. Por exemplo, por exemplo `*.contoso.com` , pode usar , ou qualquer outra corda que `sub1.contoso.com` `sub2.contoso.com` `*.contoso.com` corresponda ao padrão wildcard. Em seguida, **selecione Validate**.

    O botão **de domínio personalizado Add** está ativado.

1. Certifique-se de que **o tipo de registo do nome anfitrião** está definido para o registo **CNAME (www \. example.com ou qualquer subdomínio)**. **Selecione Adicionar domínio personalizado**.

    ![Screenshot que mostra a adição de um nome DNS à aplicação.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **de Domínios Personalizados** da aplicação. Atualize o navegador para atualizar os dados.

    <details>
        <summary>O que se passa com o rótulo de aviso <strong>"Não Seguro"?</strong></summary>
        Um rótulo de aviso para o seu domínio personalizado significa que ainda não está ligado a um certificado TLS/SSL. Qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá um erro ou aviso, dependendo do navegador. Para adicionar uma ligação TLS, consulte <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">Secure um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service</a>.
    </details>

---
    
## <a name="6-test-in-a-browser"></a>6. Teste num browser

Navegue pelos nomes DNS que configuraste anteriormente.

![Screenshot que mostra navegação para uma aplicação Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>Recebo um erro HTTP 404 (Não Encontrado).</summary>
<ul>
<li>O domínio personalizado configurado está a faltar um registo A ou um registo CNAME.</li>
<li>O cliente do browser colocou em cache o endereço IP antigo do seu domínio. Limpe a cache e volte a testar a resolução do DNS. Num computador Windows, limpe a cache com <code>ipconfig /flushdns</code>.</li>
</ul>
</details>

## <a name="migrate-an-active-domain"></a>Migrar um domínio ativo

Para migrar um site em direto e o respetivo nome de domínio DNS para o Serviço de Aplicações, consulte [Migrar um nome DNS ativo para o Serviço de Aplicações do Azure](manage-custom-dns-migrate-domain.md).

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Redirecionar para um diretório personalizado

<details>
<summary>Preciso disto?</summary>
<p>Depende da sua aplicação. Por predefinição, o Serviço de Aplicações direciona os pedidos Web para o diretório de raiz do código da sua aplicação. Mas certas estruturas web não&#39;começar no diretório de raiz. Por exemplo, o <a href="https://laravel.com/">Laravel</a> é iniciado no subdiretório <code>public</code>. Para continuar o <code>contoso.com</code> exemplo do DNS, esta aplicação é acessível em <code>http://contoso.com/public</code> , mas você pretende <code>http://contoso.com</code> dirigir-se ao <code>public</code> diretório. </p>
</details>

Embora este seja um cenário comum, não envolve mapeamento de domínio personalizado, mas trata-se de personalizar o diretório virtual dentro da sua app.

1. Selecione **as definições** de aplicação no painel esquerdo da sua página de aplicações web.

1. Na parte inferior da página, o diretório de raiz virtual `/` aponta para `site\wwwroot` por predefinição, que é o diretório de raiz do código da aplicação. Altere-o para apontar para `site\wwwroot\public`, por exemplo, e guarde as alterações.

    ![Screenshot que mostra personalizar um diretório virtual.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. Após o fim da operação, verifique navegando no caminho raiz da sua aplicação no navegador (por exemplo, `http://contoso.com` ou `http://<app-name>.azurewebsites.net` ).

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar a gestão de domínios personalizados com scripts utilizando o [Azure CLI](/cli/azure/install-azure-cli) ou [o Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>CLI do Azure

O comando seguinte adiciona um nome DNS personalizado configurado a uma aplicação do Serviço de Aplicações.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Para obter mais informações, veja [Map a custom domain to a web app](scripts/cli-configure-custom-domain.md) (Mapear um domínio personalizado a uma aplicação Web).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O comando seguinte adiciona um nome DNS personalizado configurado a uma aplicação do Serviço de Aplicações.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Para obter mais informações, veja [Assign a custom domain to a web app](scripts/powershell-configure-custom-domain.md) (Atribuir um domínio personalizado a uma aplicação Web).

## <a name="next-steps"></a>Passos seguintes

Continue até ao próximo tutorial para aprender a ligar um certificado TLS/SSL personalizado a uma aplicação web.

> [!div class="nextstepaction"]
> [Proteja um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service](configure-ssl-bindings.md)
