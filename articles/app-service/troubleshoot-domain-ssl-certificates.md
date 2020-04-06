---
title: Domínio de resolução de problemas e certificados TLS/SSL
description: Encontre soluções para os problemas comuns que poderá encontrar quando configurar um certificado de domínio ou TLS/SSL no Serviço de Aplicações Azure.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: d61b95c7136a4cbce11789a58d27cc1a164ae374
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668029"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Problemas de domínio e problemas de certificado TLS/SSL no Serviço de Aplicações Azure

Este artigo lista problemas comuns que poderá encontrar quando configura rumar um certificado de domínio ou TLS/SSL para as suas aplicações web no Azure App Service. Descreve também possíveis causas e soluções para estes problemas.

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns mSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Suporte**.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Problemas de certificado

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>Não é possível adicionar um certificado TLS/SSL vinculativo a uma aplicação 

#### <a name="symptom"></a>Sintoma

Quando adiciona uma ligação TLS, recebe a seguinte mensagem de erro:

"Não adicionou a ligação SSL. Não é possível definir certificado para VIP existente porque outro VIP já usa esse certificado."

#### <a name="cause"></a>Causa

Este problema pode ocorrer se tiver várias ligações SSL baseadas em IP para o mesmo endereço IP em várias aplicações. Por exemplo, a app A tem um SSL baseado em IP com um certificado antigo. A App B tem um SSL baseado em IP com um novo certificado para o mesmo endereço IP. Ao atualizar a aplicação TLS vinculando-se com o novo certificado, falha com este erro porque o mesmo endereço IP está a ser usado para outra aplicação. 

#### <a name="solution"></a>Solução 

Para corrigir este problema, utilize um dos seguintes métodos:

- Elimine a ligação SSL baseada em IP na aplicação que utiliza o certificado antigo. 
- Crie uma nova ligação SSL baseada em IP que utilize o novo certificado.

### <a name="you-cant-delete-a-certificate"></a>Não pode apagar um certificado 

#### <a name="symptom"></a>Sintoma

Quando tenta apagar um certificado, recebe a seguinte mensagem de erro:

"Não pode ndo apagar o certificado porque está atualmente a ser utilizado numa ligação TLS/SSL. A ligação TLS deve ser removida antes de poder eliminar o certificado."

#### <a name="cause"></a>Causa

Este problema pode ocorrer se outra aplicação usar o certificado.

#### <a name="solution"></a>Solução

Remova a ligação TLS para esse certificado das aplicações. Em seguida, tente apagar o certificado. Se ainda não conseguir apagar o certificado, limpe a cache do navegador de Internet e abra o portal Azure numa nova janela do navegador. Em seguida, tente apagar o certificado.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Não pode comprar um certificado de Serviço de Aplicações 

#### <a name="symptom"></a>Sintoma
Não é possível comprar um certificado de [Serviço de Aplicações Azure](./configure-ssl-certificate.md#import-an-app-service-certificate) no portal Azure.

#### <a name="cause-and-solution"></a>Causa e solução
Este problema pode ocorrer por qualquer uma das seguintes razões:

- O plano de Serviço de Aplicações é Gratuito ou Partilhado. Estes níveis de preços não suportam TLS. 

    **Solução**: Atualize o plano do Serviço de Aplicações para a App para Standard.

- A subscrição não tem um cartão de crédito válido.

    **Solução**: Adicione um cartão de crédito válido à sua subscrição. 

- A oferta de subscrição não suporta a aquisição de um certificado de Serviço de Aplicações, como o Microsoft Student.  

    **Solução**: Atualize a sua subscrição. 

- A subscrição atingiu o limite de compras que são permitidas numa subscrição.

    **Solução**: Os certificados do Serviço de Aplicações têm um limite de 10 compras de certificados para os tipos de subscrição Pay-As-You-Go e EA. Para outros tipos de subscrição, o limite é 3. Para aumentar o limite, contacte o [suporte azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- O certificado do Serviço de Aplicações foi marcado como fraude. Recebeu a seguinte mensagem de erro: "O seu certificado foi sinalizado por possível fraude. O pedido está atualmente a ser analisado. Se o certificado não for utilizável dentro de 24 horas, contacte o Suporte Azure."

    **Solução**: Se o certificado estiver marcado como fraude e não for resolvido após 24 horas, siga estes passos:

    1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
    2. Vá aos Certificados de Serviço de **Aplicações**e selecione o certificado.
    3. Selecione passo de **configuração** > do certificado**2: Verifique** > **a verificação do domínio**. Este passo envia um aviso de e-mail ao fornecedor de certificados Azure para resolver o problema.

## <a name="custom-domain-problems"></a>Problemas de domínio personalizados

### <a name="a-custom-domain-returns-a-404-error"></a>Um domínio personalizado devolve um erro de 404 

#### <a name="symptom"></a>Sintoma

Quando navega no site utilizando o nome de domínio personalizado, recebe a seguinte mensagem de erro:

"Error 404-Web app não encontrado."

#### <a name="cause-and-solution"></a>Causa e solução

**Causa 1** 

O domínio personalizado que configuraste está a faltar um CNAME ou um disco. 

**Solução para o motivo 1**

- Se tiver adicionado um disco A, certifique-se de que um disco TXT também é adicionado. Para mais informações, consulte [Criar o registo A](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Se não tiver de utilizar o domínio raiz para a sua aplicação, recomendamos que utilize um disco CNAME em vez de um disco A.
- Não use um disco CNAME e um disco A para o mesmo domínio. Esta questão pode causar um conflito e impedir que o domínio seja resolvido. 

**Causa 2** 

O navegador de internet ainda pode estar a gravar o antigo endereço IP para o seu domínio. 

**Solução para a Causa 2**

Limpe o navegador. Para dispositivos Windows, pode `ipconfig /flushdns`executar o comando . Utilize [WhatsmyDNS.net](https://www.whatsmydns.net/) para verificar se o seu domínio aponta para o endereço IP da aplicação. 

### <a name="you-cant-add-a-subdomain"></a>Não se pode adicionar um subdomínio 

#### <a name="symptom"></a>Sintoma

Não é possível adicionar um novo nome de anfitrião a uma aplicação para atribuir um subdomínio.

#### <a name="solution"></a>Solução

- Consulte o administrador de subscrição para se certificar de que tem permissões para adicionar um nome de anfitrião à aplicação.
- Se precisar de mais subdomínios, recomendamos que altere o domínio para o Serviço de Nome de Domínio Azure (DNS). Ao utilizar o Azure DNS, pode adicionar 500 nomes de anfitriões à sua aplicação. Para mais informações, consulte [Adicionar um subdomínio](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).

### <a name="dns-cant-be-resolved"></a>DNS não pode ser resolvido

#### <a name="symptom"></a>Sintoma

Recebeu a seguinte mensagem de erro:

"O registo do DNS não pôde ser localizado."

#### <a name="cause"></a>Causa
Este problema ocorre por uma das seguintes razões:

- O período de vida (TTL) não expirou. Verifique a configuração DNS para determinar o valor TTL e, em seguida, aguarde que o período expire.
- A configuração DNS está incorreta.

#### <a name="solution"></a>Solução
- Espere 48 horas para que este problema se resolva sozinho.
- Se conseguir alterar a definição de TTL na sua configuração DNS, altere o valor para 5 minutos para ver se isto resolve o problema.
- Utilize [WhatsmyDNS.net](https://www.whatsmydns.net/) para verificar se o seu domínio aponta para o endereço IP da aplicação. Se não o fizer, configure o registo A para o endereço IP correto da aplicação.

### <a name="you-need-to-restore-a-deleted-domain"></a>Precisa restaurar um domínio eliminado 

#### <a name="symptom"></a>Sintoma
O seu domínio já não é visível no portal Azure.

#### <a name="cause"></a>Causa 
O proprietário da subscrição pode ter acidentalmente apagado o domínio.

#### <a name="solution"></a>Solução
Se o seu domínio foi apagado há menos de sete dias, o domínio ainda não iniciou o processo de eliminação. Neste caso, pode voltar a comprar o mesmo domínio no portal Azure sob a mesma subscrição. (Certifique-se de que escreve o nome de domínio exato na caixa de pesquisa.) Não voltará a ser cobrado por este domínio. Se o domínio foi apagado há mais de sete dias, contacte o [suporte azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para ajudar na restauração do domínio.

## <a name="domain-problems"></a>Problemas de domínio

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>Comprou um certificado TLS/SSL para o domínio errado

#### <a name="symptom"></a>Sintoma

Comprou um certificado de Serviço de Aplicações para o domínio errado. Não é possível atualizar o certificado para utilizar o domínio correto.

#### <a name="solution"></a>Solução

Apague esse certificado e, em seguida, compre um novo certificado.

Se o certificado atual que usa o domínio errado estiver no estado "Emitido", também será cobrado para esse certificado. Os certificados do Serviço de Aplicações não são reembolsáveis, mas pode contactar o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para ver se existem outras opções. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Um certificado de Serviço de Aplicações foi renovado, mas a aplicação mostra o antigo certificado 

#### <a name="symptom"></a>Sintoma

O certificado de Serviço de Aplicações foi renovado, mas a aplicação que utiliza o certificado de Serviço de Aplicações ainda está a usar o certificado antigo. Além disso, recebeu um aviso de que o protocolo HTTPS é necessário.

#### <a name="cause"></a>Causa 
O Serviço de Aplicações sincroniza automaticamente o seu certificado dentro de 48 horas. Quando roda ou atualiza um certificado, por vezes o pedido ainda está a recuperar o certificado antigo e não o certificado recém-atualizado. A razão é que o trabalho para sincronizar o recurso de certificado ainda não funcionou. Clique em Sync. A operação de sincronização atualiza automaticamente as encadernações do nome de anfitrião para o certificado no Serviço de Aplicações sem causar qualquer inatividade nas suas apps.
 
#### <a name="solution"></a>Solução

Pode forçar uma sincronização do certificado:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione Certificados de Serviço de **Aplicações**e, em seguida, selecione o certificado.
2. Selecione **Rekey e Sync,** e, em seguida, selecione **Sync**. A sincronização leva algum tempo para terminar. 
3. Quando a sincronização estiver concluída, vê a seguinte notificação: "Atualizou com sucesso todos os recursos com o certificado mais recente."

### <a name="domain-verification-is-not-working"></a>A verificação de domínio não está a funcionar 

#### <a name="symptom"></a>Sintoma 
O certificado do Serviço de Aplicações requer verificação de domínio antes de o certificado estar pronto a ser utilizado. Quando selecionar **Verificar,** o processo falha.

#### <a name="solution"></a>Solução
Verifique manualmente o seu domínio adicionando um registo TXT:
 
1.  Vá ao fornecedor do Serviço de Nomes de Domínio (DNS) que acolhe o seu nome de domínio.
2.  Adicione um registo TXT para o seu domínio que utiliza o valor do símbolo de domínio que é mostrado no portal Azure. 

Aguarde alguns minutos para que a propagação do DNS se escorra e, em seguida, selecione o botão **Refresh** para desencadear a verificação. 

Como alternativa, pode utilizar o método da página web HTML para verificar manualmente o seu domínio. Este método permite à autoridade do certificado confirmar a propriedade do domínio do domínio para o que o certificado é emitido.

1.  Crie um ficheiro HTML que se chame {domain verification token}.html. O conteúdo deste ficheiro deve ser o valor da ficha de verificação de domínio.
3.  Faça upload deste ficheiro na raiz do servidor web que está hospedando o seu domínio.
4.  Selecione **Refresh** para verificar o estado do certificado. Pode levar alguns minutos para a verificação terminar.

Por exemplo, se estiver a comprar um certificado padrão para azure.com com o token de https://azure.com/1234abcd.html verificação de domínio 1234abcd, um pedido web feito deve devolver 1234abcd. 

> [!IMPORTANT]
> Uma ordem de certificado tem apenas 15 dias para completar a operação de verificação de domínio. Após 15 dias, a autoridade do certificado nega a certidão e não é cobrado pelo certificado. Nesta situação, apague este certificado e tente novamente.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Não se pode comprar um domínio

#### <a name="symptom"></a>Sintoma
Não é possível comprar um domínio do Serviço de Aplicações no portal Azure.

#### <a name="cause-and-solution"></a>Causa e solução

Este problema ocorre por uma das seguintes razões:

- Não existem cartões de crédito na subscrição do Azure ou o cartão de crédito é inválido.

    **Solução**: Adicione um cartão de crédito válido à sua subscrição.

- Não é o proprietário da subscrição, pelo que não tem permissão para comprar um domínio.

    **Solução**: [Atribuir a função proprietário](../role-based-access-control/role-assignments-portal.md) à sua conta. Ou contacte o administrador de subscrição para obter permissão para adquirir um domínio.
- Atingiu o limite de compra de domínios da subscrição. O limite atual é 20.

    **Solução**: Para solicitar um aumento do limite, contacte o [suporte azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- O tipo de subscrição do Azure não suporta a compra de um domínio do Serviço de Aplicações.

    **Solução**: Atualize a subscrição do Azure para outro tipo de subscrição, como uma subscrição Pay-As-You-Go.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Não pode adicionar um nome de anfitrião a uma aplicação 

#### <a name="symptom"></a>Sintoma

Quando adiciona um nome de anfitrião, o processo não valida e verifica o domínio.

#### <a name="cause"></a>Causa 

Este problema ocorre por uma das seguintes razões:

- Não tem permissão para adicionar um nome de anfitrião.

    **Solução**: Peça ao administrador de subscrição que lhe dê permissão para adicionar um nome de anfitrião.
- A sua propriedade de domínio não pôde ser verificada.

    **Solução**: Verifique se o seu registo CNAME ou A está configurado corretamente. Para mapear um domínio personalizado para uma aplicação, crie um disco CNAME ou um disco A. Se pretender utilizar um domínio raiz, deve utilizar registos A e TXT:

    |Tipo de registo|Anfitrião|Apontar para|
    |------|------|-----|
    |A|@|Endereço IP para uma aplicação|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>FAQ

**Tenho de configurar o meu domínio personalizado para o meu website assim que o comprar?**

Ao adquirir um domínio no portal Azure, a aplicação do Serviço de Aplicações é configurada automaticamente para utilizar esse domínio personalizado. Não tens de dar passos adicionais. Para mais informações, assista a Self Help do Serviço de [Aplicações Azure: Adicione um nome](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) de domínio personalizado no Canal9.

**Posso usar um domínio comprado no portal Azure para apontar para um VM Azure?**

Sim, pode apontar o domínio para um VM. Para obter mais informações, veja [Utilizar o DNS do Azure para oferecer definições de domínio personalizado para um serviço do Azure](../dns/dns-custom-domain.md).

**O meu domínio é hospedado pelo GoDaddy ou pelo Azure DNS?**

Os domínios do serviço de aplicações usam o GoDaddy para o registo de domínioe o Azure DNS para acolher os domínios. 

**Tenho renovação automática ativada, mas ainda recebi um aviso de renovação para o meu domínio por e-mail. O que devo fazer?**

Se tiver uma renovação automática ativada, não precisa de tomar qualquer medida. O e-mail de aviso é fornecido para informá-lo de que o domínio está perto de expirar e para renovar manualmente se não estiver ativado a renovação automática.

**Vou ser cobrado pelo Azure DNS que acolhe o meu domínio?**

O custo inicial da compra de domínio aplica-se apenas ao registo de domínio. Além do custo de registo, existem encargos incorridos para o DNS Azure com base na sua utilização. Para mais informações, consulte [os preços do DNS azure](https://azure.microsoft.com/pricing/details/dns/) para obter mais detalhes.

**Comprei o meu domínio mais cedo no portal Azure e quero mudar-me do GoDaddy para o anfitrião do DNS Azure. Como posso fazer isto?**

Não é obrigatório migrar para o dNS azure hospedado. Se quiser migrar para o Azure DNS, a experiência de gestão de domínios no portal Azure sobre fornece informações sobre as etapas necessárias para se deslocar para O DNS Azure. Se o domínio foi adquirido através do App Service, a migração do GoDaddy hospedado para o DNS Azure é um procedimento relativamente perfeito.

**Gostaria de comprar o meu domínio no Domínio do Serviço de Aplicações, mas posso hospedar o meu domínio no GoDaddy em vez do Azure DNS?**

A partir de 24 de julho de 2017, os domínios do App Service adquiridos no portal estão hospedados no Azure DNS. Se preferir utilizar um fornecedor de hospedagem diferente, deve ir ao seu website para obter uma solução de hospedagem de domínio.

**Tenho de pagar a proteção da privacidade pelo meu domínio?**

Ao adquirir um domínio através do portal Azure, pode optar por adicionar privacidade sem custos adicionais. Este é um dos benefícios de comprar o seu domínio através do Azure App Service.

**Se eu decidir que não quero mais o meu domínio, posso ter o meu dinheiro de volta?**

Ao comprar um domínio, não é cobrado por um período de cinco dias, período durante o qual pode decidir que não quer o domínio. Se decidir que não quer o domínio dentro desse período de cinco dias, não será cobrado. (.os domínios do Reino Unido são uma exceção a isto. Se comprar um domínio .uk, é cobrado imediatamente e não pode ser reembolsado.)

**Posso usar o domínio em outra aplicação do Serviço de Aplicações Azure na minha subscrição?**

Sim. Ao aceder aos Domínios Personalizados e à lâmina TLS no portal Azure, vê os domínios que adquiriu. Pode configurar a sua aplicação para utilizar qualquer um desses domínios.

**Posso transferir um domínio de uma subscrição para outra subscrição?**

Pode mover um domínio para outro grupo de subscrição/recursos utilizando o cmdlet [Move-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) PowerShell.

**Como posso gerir o meu domínio personalizado se não tenho atualmente uma aplicação azure App Service?**

Pode gerir o seu domínio mesmo que não tenha uma Aplicação Web do Serviço de Aplicações. O domínio pode ser utilizado para serviços Azure como máquina virtual, armazenamento etc. Se pretende utilizar o domínio para aplicações web do serviço de aplicações, então precisa de incluir uma Aplicação Web que não esteja no plano de Serviço de Aplicações Gratuitos para ligar o domínio à sua aplicação web.

**Posso mover uma aplicação web com um domínio personalizado para outra subscrição ou de App Service Environment v1 para V2?**

Sim, pode mover a sua aplicação web através de subscrições. Siga a orientação em [Como movimentar recursos em Azure.](../azure-resource-manager/management/move-resource-group-and-subscription.md) Existem algumas limitações ao mover a aplicação web. Para mais informações, consulte [Limitações para movimentar recursos](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)do Serviço de Aplicações .

Depois de mover a aplicação web, as ligações de nome do anfitrião dos domínios dentro da definição de domínios personalizados devem permanecer as mesmas. Não são necessários passos adicionais para configurar as encadernações do nome do anfitrião.
