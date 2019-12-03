---
title: Solucionar problemas de domínio e certificados SSL
description: Encontre soluções para os problemas comuns que você pode encontrar ao configurar um domínio ou certificado SSL no serviço Azure App.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 2260dddd74d7ed64eb19158a5360ed2e4c09b4a9
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688348"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-app-service"></a>Solucionar problemas de domínio e certificado SSL no serviço Azure App

Este artigo lista os problemas comuns que você pode encontrar ao configurar um domínio ou certificado SSL para seus aplicativos Web no serviço Azure App. Ele também descreve as possíveis causas e soluções para esses problemas.

Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure nos [fóruns do MSDN e do Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Problemas de certificado

### <a name="you-cant-add-an-ssl-certificate-binding-to-an-app"></a>Não é possível adicionar uma associação de certificado SSL a um aplicativo 

#### <a name="symptom"></a>Sintoma

Ao adicionar uma associação SSL, você recebe a seguinte mensagem de erro:

"Falha ao adicionar Associação SSL. Não é possível definir o certificado para o VIP existente porque outro VIP já usa esse certificado. "

#### <a name="cause"></a>Causa

Esse problema pode ocorrer se você tiver várias associações SSL baseadas em IP para o mesmo endereço IP em vários aplicativos. Por exemplo, o aplicativo A tem um SSL baseado em IP com um certificado antigo. O aplicativo B tem um SSL baseado em IP com um novo certificado para o mesmo endereço IP. Quando você atualiza a associação SSL do aplicativo com o novo certificado, ele falha com esse erro porque o mesmo endereço IP está sendo usado para outro aplicativo. 

#### <a name="solution"></a>Solução 

Para corrigir esse problema, use um dos seguintes métodos:

- Exclua a associação SSL baseada em IP no aplicativo que usa o certificado antigo. 
- Crie uma nova associação SSL com base em IP que usa o novo certificado.

### <a name="you-cant-delete-a-certificate"></a>Não é possível excluir um certificado 

#### <a name="symptom"></a>Sintoma

Ao tentar excluir um certificado, você recebe a seguinte mensagem de erro:

"Não é possível excluir o certificado porque ele está sendo usado atualmente em uma associação SSL. A associação SSL deve ser removida antes que você possa excluir o certificado. "

#### <a name="cause"></a>Causa

Esse problema pode ocorrer se outro aplicativo usar o certificado.

#### <a name="solution"></a>Solução

Remova a associação SSL para esse certificado dos aplicativos. Em seguida, tente excluir o certificado. Se você ainda não pode excluir o certificado, limpe o cache do navegador da Internet e reabra o portal do Azure em uma nova janela do navegador. Em seguida, tente excluir o certificado.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Não é possível comprar um certificado do serviço de aplicativo 

#### <a name="symptom"></a>Sintoma
Não é possível comprar um [certificado de serviço Azure app](./configure-ssl-certificate.md#import-an-app-service-certificate) do portal do Azure.

#### <a name="cause-and-solution"></a>Causa e solução
Esse problema pode ocorrer por qualquer um dos seguintes motivos:

- O plano do serviço de aplicativo é gratuito ou compartilhado. Esses tipos de preço não dão suporte a SSL. 

    **Solução**: Atualize o plano do serviço de aplicativo para o aplicativo para Standard.

- A assinatura não tem um cartão de crédito válido.

    **Solução**: Adicione um cartão de crédito válido à sua assinatura. 

- A oferta de assinatura não dá suporte à compra de um certificado do serviço de aplicativo, como o aluno da Microsoft.  

    **Solução**: Atualize sua assinatura. 

- A assinatura atingiu o limite de compras permitidas em uma assinatura.

    **Solução**: os certificados do serviço de aplicativo têm um limite de 10 compras de certificado para os tipos de assinatura pago pelo uso e ea. Para outros tipos de assinatura, o limite é 3. Para aumentar o limite, entre em contato com o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- O certificado do serviço de aplicativo foi marcado como fraude. Você recebeu a seguinte mensagem de erro: "o certificado foi sinalizado para uma possível fraude. A solicitação está em análise no momento. Se o certificado não se tornar utilizável dentro de 24 horas, entre em contato com o suporte do Azure.

    **Solução**: se o certificado for marcado como fraude e não for resolvido após 24 horas, siga estas etapas:

    1. Inicie sessão no [portal do Azure](https://portal.azure.com).
    2. Vá para **certificados do serviço de aplicativo**e selecione o certificado.
    3. Selecione **configuração de certificado** > **etapa 2: verificar** > **verificação de domínio**. Esta etapa envia um aviso por email ao provedor de certificados do Azure para resolver o problema.

## <a name="custom-domain-problems"></a>Problemas de domínio personalizado

### <a name="a-custom-domain-returns-a-404-error"></a>Um domínio personalizado retorna um erro 404 

#### <a name="symptom"></a>Sintoma

Ao navegar até o site usando o nome de domínio personalizado, você receberá a seguinte mensagem de erro:

"Erro 404-aplicativo Web não encontrado."

#### <a name="cause-and-solution"></a>Causa e solução

**Causa 1** 

O domínio personalizado que você configurou está sem um CNAME ou um registro. 

**Solução para o motivo 1**

- Se você adicionou um registro A, certifique-se de que um registro TXT também seja adicionado. Para obter mais informações, consulte [criar um registro a](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Se você não precisar usar o domínio raiz para seu aplicativo, é recomendável usar um registro CNAME em vez de um registro A.
- Não use um registro CNAME e um registro A para o mesmo domínio. Esse problema pode causar um conflito e impedir que o domínio seja resolvido. 

**Causa 2** 

O navegador da Internet ainda pode estar armazenando em cache o endereço IP antigo para seu domínio. 

**Solução para a causa 2**

Limpe o navegador. Para dispositivos Windows, você pode executar o comando `ipconfig /flushdns`. Use [WhatsmyDNS.net](https://www.whatsmydns.net/) para verificar se o seu domínio aponta para o endereço IP do aplicativo. 

### <a name="you-cant-add-a-subdomain"></a>Não é possível adicionar um subdomínio 

#### <a name="symptom"></a>Sintoma

Você não pode adicionar um novo nome de host a um aplicativo para atribuir um subdomínio.

#### <a name="solution"></a>Solução

- Verifique com o administrador da assinatura para certificar-se de que você tem permissões para adicionar um nome de host ao aplicativo.
- Se precisar de mais subdomínios, recomendamos que você altere o domínio de hospedagem para o serviço de nomes de domínio do Azure (DNS). Usando o DNS do Azure, você pode adicionar nomes de host 500 ao seu aplicativo. Para obter mais informações, consulte [Adicionar um subdomínio](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).

### <a name="dns-cant-be-resolved"></a>O DNS não pode ser resolvido

#### <a name="symptom"></a>Sintoma

Você recebeu a seguinte mensagem de erro:

"O registro DNS não pôde ser localizado."

#### <a name="cause"></a>Causa
Esse problema ocorre por um dos seguintes motivos:

- O período de vida útil (TTL) não expirou. Verifique a configuração de DNS para seu domínio para determinar o valor de TTL e aguarde até que o período expire.
- A configuração de DNS está incorreta.

#### <a name="solution"></a>Solução
- Aguarde 48 horas para que esse problema se resolva.
- Se você puder alterar a configuração de TTL em sua configuração de DNS, altere o valor para 5 minutos para ver se isso resolve o problema.
- Use [WhatsmyDNS.net](https://www.whatsmydns.net/) para verificar se o seu domínio aponta para o endereço IP do aplicativo. Se não estiver, configure o registro a para o endereço IP correto do aplicativo.

### <a name="you-need-to-restore-a-deleted-domain"></a>Você precisa restaurar um domínio excluído 

#### <a name="symptom"></a>Sintoma
Seu domínio não está mais visível no portal do Azure.

#### <a name="cause"></a>Causa 
O proprietário da assinatura pode ter excluído o domínio acidentalmente.

#### <a name="solution"></a>Solução
Se o domínio tiver sido excluído há menos de sete dias, o domínio ainda não iniciou o processo de exclusão. Nesse caso, você pode comprar o mesmo domínio novamente na portal do Azure na mesma assinatura. (Certifique-se de digitar o nome de domínio exato na caixa de pesquisa.) Você não será cobrado novamente para este domínio. Se o domínio tiver sido excluído há mais de sete dias, entre em contato com o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter ajuda com a restauração do domínio.

## <a name="domain-problems"></a>Problemas de domínio

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Você comprou um certificado SSL para o domínio errado

#### <a name="symptom"></a>Sintoma

Você comprou um certificado do serviço de aplicativo para o domínio errado. Você não pode atualizar o certificado para usar o domínio correto.

#### <a name="solution"></a>Solução

Exclua esse certificado e, em seguida, compre um novo certificado.

Se o certificado atual que usa o domínio errado estiver no estado "emitido", você também será cobrado pelo certificado. Os certificados do serviço de aplicativo não são refundable, mas você pode entrar em contato com o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para ver se há outras opções. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Um certificado do serviço de aplicativo foi renovado, mas o aplicativo mostra o certificado antigo 

#### <a name="symptom"></a>Sintoma

O certificado do serviço de aplicativo foi renovado, mas o aplicativo que usa o certificado do serviço de aplicativo ainda está usando o certificado antigo. Além disso, você recebeu um aviso de que o protocolo HTTPS é necessário.

#### <a name="cause"></a>Causa 
O serviço de aplicativo sincroniza automaticamente seu certificado dentro de 48 horas. Quando você gira ou atualiza um certificado, às vezes o aplicativo ainda está recuperando o certificado antigo e não o certificado recém atualizado. O motivo é que o trabalho para sincronizar o recurso de certificado ainda não foi executado. Clique em sincronizar. A operação de sincronização atualiza automaticamente as associações de nome de host para o certificado no serviço de aplicativo sem causar nenhum tempo de inatividade para seus aplicativos.
 
#### <a name="solution"></a>Solução

Você pode forçar uma sincronização do certificado:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **certificados do serviço de aplicativo**e, em seguida, selecione o certificado.
2. Selecione **rechaveamento e sincronização**e, em seguida, selecione **sincronizar**. A sincronização leva algum tempo para ser concluída. 
3. Quando a sincronização for concluída, você verá a seguinte notificação: "atualização bem-sucedida de todos os recursos com o certificado mais recente".

### <a name="domain-verification-is-not-working"></a>A verificação de domínio não está funcionando 

#### <a name="symptom"></a>Sintoma 
O certificado do serviço de aplicativo requer a verificação de domínio antes que o certificado esteja pronto para uso. Quando você seleciona **verificar**, o processo falha.

#### <a name="solution"></a>Solução
Verifique manualmente seu domínio adicionando um registro TXT:
 
1.  Vá para o provedor DNS (serviço de nomes de domínio) que hospeda seu nome de domínio.
2.  Adicione um registro TXT para seu domínio que usa o valor do token de domínio mostrado no portal do Azure. 

Aguarde alguns minutos para que a propagação de DNS seja executada e, em seguida, selecione o botão **Atualizar** para disparar a verificação. 

Como alternativa, você pode usar o método HTML da página da Web para verificar manualmente seu domínio. Esse método permite que a autoridade de certificação confirme a propriedade do domínio do domínio para o qual o certificado é emitido.

1.  Crie um arquivo HTML chamado {token de verificação de domínio}. html. O conteúdo desse arquivo deve ser o valor do token de verificação de domínio.
3.  Carregue esse arquivo na raiz do servidor Web que está hospedando seu domínio.
4.  Selecione **Atualizar** para verificar o status do certificado. Pode levar alguns minutos para que a verificação seja concluída.

Por exemplo, se você estiver comprando um certificado padrão para azure.com com o token de verificação de domínio 1234abcd, uma solicitação da Web feita para https://azure.com/1234abcd.html deverá retornar 1234abcd. 

> [!IMPORTANT]
> Uma ordem de certificado tem apenas 15 dias para concluir a operação de verificação de domínio. Após 15 dias, a autoridade de certificação negará o certificado e você não será cobrado pelo certificado. Nessa situação, exclua esse certificado e tente novamente.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Não é possível comprar um domínio

#### <a name="symptom"></a>Sintoma
Não é possível comprar um domínio do serviço de aplicativo no portal do Azure.

#### <a name="cause-and-solution"></a>Causa e solução

Esse problema ocorre por um dos seguintes motivos:

- Não existem cartões de crédito na subscrição do Azure ou o cartão de crédito é inválido.

    **Solução**: Adicione um cartão de crédito válido à sua assinatura.

- Não é o proprietário da subscrição, pelo que não tem permissão para comprar um domínio.

    **Solução**: [atribua a função de proprietário](../role-based-access-control/role-assignments-portal.md) à sua conta. Ou contate o administrador da assinatura para obter permissão para comprar um domínio.
- Atingiu o limite de compra de domínios da subscrição. O limite atual é 20.

    **Solução**: para solicitar um aumento do limite, entre em contato com o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- O tipo de subscrição do Azure não suporta a compra de um domínio do Serviço de Aplicações.

    **Solução**: Atualize sua assinatura do Azure para outro tipo de assinatura, como uma assinatura paga conforme o uso.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Não é possível adicionar um nome de host a um aplicativo 

#### <a name="symptom"></a>Sintoma

Quando você adiciona um nome de host, o processo falha ao validar e verificar o domínio.

#### <a name="cause"></a>Causa 

Esse problema ocorre por um dos seguintes motivos:

- Você não tem permissão para adicionar um nome de host.

    **Solução**: peça ao administrador da assinatura que lhe conceda permissão para adicionar um nome de host.
- Não foi possível verificar a propriedade do domínio.

    **Solução**: Verifique se o CNAME ou registro a está configurado corretamente. Para mapear um domínio personalizado para um aplicativo, crie um registro CNAME ou um registro A. Se você quiser usar um domínio raiz, deverá usar os registros A e TXT:

    |Tipo de registo|Host|Aponte para|
    |------|------|-----|
    |A|@|Endereço IP para um aplicativo|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>FAQ

**É necessário configurar meu domínio personalizado para meu site depois de comprá-lo?**

Quando você adquire um domínio do portal do Azure, o aplicativo do serviço de aplicativo é configurado automaticamente para usar esse domínio personalizado. Você não precisa executar nenhuma etapa adicional. Para obter mais informações, Assista à [auto-ajuda do serviço de Azure App: adicionar um nome de domínio personalizado](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) em channel9.

**Posso usar um domínio adquirido no portal do Azure para apontar para uma VM do Azure em vez disso?**

Sim, você pode apontar o domínio para uma VM. Para obter mais informações, veja [Utilizar o DNS do Azure para oferecer definições de domínio personalizado para um serviço do Azure](../dns/dns-custom-domain.md).

**O meu domínio é hospedado pelo GoDaddy ou pelo DNS do Azure?**

Os domínios do serviço de aplicativo usam GoDaddy para o registro de domínio e o DNS do Azure para hospedar os domínios. 

**Tenho a renovação automática habilitada, mas ainda recebeva um aviso de renovação para o meu domínio por email. O que devo fazer?**

Se você tiver a renovação automática habilitada, não será necessário realizar nenhuma ação. O email de aviso é fornecido para informar que o domínio está perto de expirar e para renovar manualmente se a renovação automática não estiver habilitada.

**Serei cobrado pelo DNS do Azure que hospeda meu domínio?**

O custo inicial da compra de domínio se aplica somente ao registro de domínio. Além do custo de registro, há encargos incorridos para o DNS do Azure com base no seu uso. Para obter mais informações, consulte [preços do DNS do Azure](https://azure.microsoft.com/pricing/details/dns/) para obter mais detalhes.

**Comprei meu domínio anteriormente na portal do Azure e desejamos mudar da hospedagem GoDaddy para a hospedagem de DNS do Azure. Como posso fazer isso?**

Não é obrigatório migrar para a hospedagem de DNS do Azure. Se você quiser migrar para o DNS do Azure, a experiência de gerenciamento de domínio no portal do Azure sobre fornece informações sobre as etapas necessárias para migrar para o DNS do Azure. Se o domínio foi adquirido por meio do serviço de aplicativo, a migração da hospedagem do GoDaddy para o DNS do Azure é um procedimento relativamente simples.

**Eu gostaria de comprar meu domínio do domínio do serviço de aplicativo, mas posso hospedar meu domínio em GoDaddy em vez de DNS do Azure?**

A partir de 24 de julho de 2017, os domínios do serviço de aplicativo adquiridos no portal são hospedados no DNS do Azure. Se preferir usar um provedor de hospedagem diferente, você deverá acessar seu site para obter uma solução de Hospedagem de domínio.

**É necessário pagar pela proteção de privacidade para meu domínio?**

Ao comprar um domínio por meio do portal do Azure, você pode optar por adicionar privacidade sem custo adicional. Essa é uma das vantagens de comprar seu domínio por meio do serviço de Azure App.

**Se eu decidir que não quero mais meu domínio, posso me repassar ao meu dinheiro?**

Quando você adquire um domínio, não é cobrado por um período de cinco dias, durante o qual você pode decidir que não deseja o domínio. Se você decidir que não quer o domínio dentro desse período de cinco dias, não será cobrado. (. os domínios do Reino Unido são uma exceção a isso. Se você comprar um domínio. Reino Unido, você será cobrado imediatamente e não poderá ser reembolsado.)

**Posso usar o domínio em outro aplicativo de serviço Azure App em minha assinatura?**

Sim. Ao acessar a folha domínios personalizados e SSL no portal do Azure, você vê os domínios que comprou. Você pode configurar seu aplicativo para usar qualquer um desses domínios.

**Posso transferir um domínio de uma assinatura para outra assinatura?**

Você pode mover um domínio para outro grupo de recursos/assinatura usando o cmdlet do PowerShell [move-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) .

**Como posso gerenciar meu domínio personalizado se não tiver atualmente um aplicativo de serviço de Azure App?**

Você pode gerenciar seu domínio mesmo que não tenha um aplicativo Web do serviço de aplicativo. O domínio pode ser usado para serviços do Azure como máquina virtual, armazenamento, etc. Se você pretende usar o domínio para aplicativos Web do serviço de aplicativo, precisará incluir um aplicativo Web que não esteja no plano de Serviço de Aplicativo Gratuito para associar o domínio ao seu aplicativo Web.

**Posso mover um aplicativo Web com um domínio personalizado para outra assinatura ou de Ambiente do Serviço de Aplicativo v1 para v2?**

Sim, você pode mover seu aplicativo Web entre assinaturas. Siga as orientações em [como mover recursos no Azure](../azure-resource-manager/resource-group-move-resources.md). Há algumas limitações ao mover o aplicativo Web. Para obter mais informações, consulte [limitações para mover os recursos do serviço de aplicativo](../azure-resource-manager/move-limitations/app-service-move-limitations.md).

Depois de mover o aplicativo Web, as associações de nome de host dos domínios dentro da configuração domínios personalizados devem permanecer as mesmas. Nenhuma etapa adicional é necessária para configurar as associações de nome de host.
