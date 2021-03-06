---
title: Tutorial - Configurar HTTPS em um domínio personalizado para Azure Front Door | Microsoft Docs
description: Neste tutorial, aprende a ativar e desativar HTTPS na configuração da porta frontal Azure para um domínio personalizado.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: duau
ms.openlocfilehash: 4291a7d46c723f799cf9d09ca0e7a3f6d614971f
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389745"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>Tutorial: Configurar HTTPS num domínio personalizado do Front Door

Este tutorial mostra como ativar o protocolo HTTPS num domínio personalizado associado ao Front Door na secção de sistemas anfitrião de front-end. Ao utilizar o protocolo HTTPS no seu domínio personalizado (por exemplo, https: \/ /www.contoso.com), certifique-se de que os seus dados sensíveis são entregues de forma segura através da encriptação TLS/SSL quando são enviados através da internet. Quando o browser está ligado a um site por HTTPS, valida o certificado de segurança do site e verifica que este é emitido por uma autoridade de certificação legítima. Este processo oferece segurança e protege as suas aplicações Web de ataques.

A porta frontal Azure suporta HTTPS num nome de anfitrião padrão da porta frontal, por padrão. Por exemplo, se criar uma Porta frontal `https://contoso.azurefd.net` (como), HTTPS está automaticamente ativada para pedidos feitos a `https://contoso.azurefd.net` . No entanto, uma vez a bordo do domínio personalizado 'www.contoso.com' terá de ativar adicionalmente HTTPS para este anfitrião frontend.   

Alguns dos principais atributos da funcionalidade HTTPS personalizada são:

- Sem custos adicionais: Não há custos para aquisição ou renovação de certificados e nenhum custo extra para o tráfego HTTPS. 

- Ativação simples: está disponível no [portal do Azure](https://portal.azure.com) o aprovisionamento de um clique. Também pode utilizar a API REST ou outras ferramentas de programador para ativar a funcionalidade.

- Está disponível a gestão de certificados completa: todas as atividades de aprovisionamento e gestão de certificados são feitas por si. Os certificados são automaticamente a provisionados e renovados antes do termo, o que elimina os riscos de interrupção do serviço devido à caducidade de um certificado.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Ativar o protocolo HTTPS no domínio personalizado.
> - Utilizar um certificado gerido por AFD 
> - Use o seu próprio certificado, isto é, um certificado TLS/SSL personalizado
> - Validar o domínio
> - Desativar o protocolo HTTPS no seu domínio personalizado


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para poder concluir os passos neste tutorial, tem primeiro de criar um Front Door com, pelo menos, um domínio personalizado carregado. Para obter mais informações, veja [Tutorial: Adicionar um domínio personalizado ao Front Door](front-door-custom-domain.md).

## <a name="tlsssl-certificates"></a>Certificados TLS/SSL

Para ativar o protocolo HTTPS para a entrega segura de conteúdos num domínio personalizado da Porta Frontal, deve utilizar um certificado TLS/SSL. Pode optar por utilizar um certificado que seja gerido pela Azure Front Door ou utilizar o seu próprio certificado.


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>Opção 1 (predefinição): utilizar um certificado gerido pelo Front Door

Quando utiliza um certificado gerido pela Porta Frontal Azure, a função HTTPS pode ser ligada com apenas alguns cliques. A Azure Front Door lida completamente com tarefas de gestão de certificados, tais como aquisição e renovação. Depois de ativar a funcionalidade, o processo é iniciado imediatamente. Se o domínio personalizado já estiver mapeado para o sistema anfitrião de font-end predefinido do Front Door (`{hostname}.azurefd.net`), não é necessária mais nenhuma ação. O Front Door vai processar os passos e concluir o pedido automaticamente. No entanto, se o domínio personalizado estiver mapeado noutro local, terá de utilizar o e-mail para validar a propriedade do domínio.

Para ativar o HTTPS num domínio personalizado, siga estes passos:

1. No [portal do Azure](https://portal.azure.com), navegue até ao perfil **Front Door**.

2. Na lista de sistemas anfitrião de front-end, selecione o domínio personalizado no qual pretende ativar HTTPS para conter o seu domínio personalizado.

3. Sob a secção **Domínio Personalizado HTTPS**, selecione **Ativado** e selecione **Porta frontal gerida** como fonte de certificado.

4. Selecione Guardar.

5. Continue a [validar o domínio.](#validate-the-domain)

> [!NOTE]
> * Para os certificados geridos pela AFD, o limite de caracteres de DigiCert é aplicado. A validação falhará se esse limite for ultrapassado.
> * Permitir o HTTPS via Front Door não é suportado para domínios ápice/raiz (exemplo: contoso.com). Pode usar o seu próprio certificado para este cenário.  Por favor, continue com a Opção 2 para mais detalhes.

### <a name="option-2-use-your-own-certificate"></a>Opção 2: utilizar o seu próprio certificado

Pode utilizar o seu próprio certificado para ativar a funcionalidade HTTPS. Este processo é efetuado através de uma integração com o Azure Key Vault, o que lhe permite armazenar os certificados de forma segura. A porta frontal Azure usa este mecanismo seguro para obter o seu certificado e requer alguns passos extras. Ao criar o seu certificado TLS/SSL, deve criá-lo com uma autoridade de certificados permitida (CA). Caso contrário, se utilizar uma AC não permitida, o pedido será rejeitado. Para obter uma lista de CAs [autorizados, consulte as autoridades de certificados autorizados para permitir https personalizados na porta frontal do Azure](front-door-troubleshoot-allowed-ca.md).

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Prepare a conta e o certificado do Azure Key Vault
 
1. Azure Key Vault: tem de ter uma conta do Azure Key Vault em execução na mesma subscrição que o Front Door para o qual pretende ativar HTTPS personalizado. Se não tiver uma, crie uma conta do Azure Key Vault.

> [!WARNING]
> A Azure Front Door suporta atualmente apenas contas key vault na mesma subscrição que a configuração da porta frontal. Escolher um Key Vault numa subscrição diferente do que o Front Door resultará numa falha.

2. Certificados do Azure Key Vault: se já tiver um certificado, poderá carregá-lo diretamente na conta do Azure Key Vault ou pode criar um novo diretamente através do Azure Key Vault a partir de uma das CAs do parceiro com o qual o Azure Key Vault está integrado. Faça o upload do seu certificado como objeto de **certificado,** em vez de **um segredo.**

> [!NOTE]
> Para o seu próprio certificado TLS/SSL, a Porta Frontal não suporta certificados com algoritmos de criptografia EC.

#### <a name="register-azure-front-door"></a>Registar porta frontal Azure

Registe o principal de serviço da Azure Front Door como uma aplicação no seu Azure Ative Directory via PowerShell.

> [!NOTE]
> Esta ação requer permissões de Administrador Global, e precisa ser executada apenas **uma vez** por inquilino.

1. Caso seja necessário, instale o [Azure PowerShell](/powershell/azure/install-az-ps) no PowerShell no seu computador local.

2. No PowerShell, execute o seguinte comando:

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Grant Azure Front Door acesso ao seu cofre chave
 
Conceda permissão à Porta Frontal Azure para aceder aos certificados na sua conta Azure Key Vault.

1. Na sua conta do cofre de chaves, em DEFINIÇÕES, selecione **Políticas de acesso** e **Adicionar nova** para criar uma nova política.

2. Em **Selecionar principal**, procure **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037** e escolha **Microsoft.Azure.Frontdoor**. Clique em **Selecionar**.

3. Em **permissões secretas,** selecione **Obter** para permitir que a Porta Frontal recupere o certificado.

4. Nas **permissões de Certificado,** selecione **Obter** para permitir que a Porta Frontal recupere o certificado.

5. Selecione **OK**. 

    A porta frontal Azure pode agora aceder a este Cofre de Chaves e aos certificados que estão armazenados neste Cofre de Chaves.
 
#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Selecione o certificado para Azure Front Door para implementar
 
1. Volte ao seu Front Door no portal. 

2. Na lista de domínios personalizados, selecione o domínio personalizado no qual pretende ativar o HTTPS.

    É apresentada a página **Domínio personalizado**.

3. Em Tipo de gestão de certificado, selecione **Utilizar o meu próprio certificado**. 

4. A porta frontal Azure requer que a subscrição da conta Key Vault seja a mesma que para a sua Porta da Frente. Selecione um cofre chave, versão Secret e Secret.

    Azure Front Door lista as seguintes informações: 
    - As contas do cofre de chaves do ID da sua subscrição. 
    - Os segredos debaixo do cofre de chaves selecionado. 
    - As versões secretas disponíveis.

    > [!NOTE]
    >  Para que o certificado seja automaticamente rodado para a versão mais recente quando uma versão mais recente do certificado estiver disponível no seu Cofre-Chave, por favor, desa um ponto final na versão secreta. Se for selecionada uma versão específica, terá de voltar a selecionar a nova versão manualmente para a rotação do certificado. Leva até 24 horas para que a nova versão do certificado/segredo seja implantada. 
 
5. Quando se usa o seu próprio certificado, a validação de domínio não é necessária. Continue a [aguardar a propagação.](#wait-for-propagation)

## <a name="validate-the-domain"></a>Validar o domínio

Se já tiver um domínio personalizado em uso que seja mapeado para o seu ponto final personalizado com um registo CNAME ou se estiver a usar o seu próprio certificado, continue a [fazer o domínio Personalizado para a sua Porta frontal](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record). Caso contrário, se a entrada de registo CNAME para o seu domínio já não existir ou contiver o subdomínio afdverify, continue a [fazer o domínio Personalizado não está mapeado para a sua porta frontal](#custom-domain-is-not-mapped-to-your-front-door).

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>O domínio personalizado está mapeado para o Front Door por um registo CNAME

Quando adicionou um domínio personalizado aos sistemas anfitrião de front-end do Front Door, criou um registo CNAME na tabela de DNS da sua entidade de registo de domínios para mapeá-lo para o nome de anfitrião .azurefd.net predefinido do Front Door. Se esse registo CNAME ainda existir e não contiver o subdomínio afdverify, a DigiCert Certificate Authority utiliza-o para validar automaticamente a propriedade do seu domínio personalizado. 

Se estiver a usar o seu próprio certificado, a validação de domínio não é necessária.

O registo CNAME deve estar no seguinte formato, em que *Nome* é o nome do seu domínio personalizado e *Valor* é o nome de anfitrião .azurefd.net predefinido do Front Door:

| Nome            | Tipo  | Valor                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

Para obter mais informações sobre os registos CNAME, veja [Criar o registo DNS CNAME](../cdn/cdn-map-content-to-custom-domain.md).

Se o registo CNAME estiver no formato correto, DigiCert verifica o seu nome de domínio personalizado automaticamente e cria um certificado dedicado para o seu nome de domínio. DigitCert não lhe enviará um e-mail de verificação e não terá de aprovar o seu próprio pedido. O certificado é válido por um ano e será auto-autoriado antes de expirar. Continue a [aguardar a propagação.](#wait-for-propagation) 

Normalmente, a validação automática demora alguns minutos. Se não vir o seu domínio validado ao fim de uma hora, abra um pedido de suporte.

>[!NOTE]
>Se tiver um registo Autorização de Autoridade de Certificação (CAA) com o seu fornecedor de DNS, tem de incluir DigiCert como AC válida. O registo CAA permite aos proprietários de domínios especificar junto dos respetivos fornecedores de DNS que ACs têm autorização para emitir certificados para os seus domínios. Se uma AC receber um pedido de certificado para um domínio que tenha um registo CAA e essa AC não estiver listada como emissora autorizada, estará proibida de emitir o certificado para esse domínio ou subdomínio. Para obter informações sobre como gerir registos CAA, veja [Manage CAA records](https://support.dnsimple.com/articles/manage-caa-record/) (Gerir registos CAA). Para obter uma ferramenta de registo CAA, veja [CAA Record Helper](https://sslmate.com/caa/) (Ajuda para Registos CAA).

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>O domínio personalizado não está mapeado para o Front Door

Se a entrada do registo CNAME para o seu ponto final já não existir ou contiver o subdomínio afdverify, siga o resto das instruções neste passo.

Depois de ativar o HTTPS no domínio personalizado, a CA do DigiCert valida a propriedade do seu domínio ao contactar a entidade de registo do mesmo, de acordo com as respetivas informações de registo em [WHOIS](http://whois.domaintools.com/). O contacto é feito por endereço de e-mail (por predefinição) ou pelo número de telefone indicado no registo em WHOIS. Antes de o HTTPS estar ativo no domínio personalizado, tem de concluir a validação do domínio. Tem seis dias úteis para aprovar o domínio. Os pedidos que não são aprovados no prazo de seis dias úteis são automaticamente cancelados. 

![Registo WHOIS](./media/front-door-custom-domain-https/whois-record.png)

A DigiCert também envia um e-mail de verificação para outros endereços de e-mail. Se as informações do registo em WHOIS forem privadas, confirme que pode aprovar diretamente a partir de um dos endereços seguintes:

admin@&lt;o-seu-nome-de-domínio.com&gt;  
administrador@&lt;o-seu-nome-de-domínio.com&gt;  
webmaster@&lt;o-seu-nome-de-domínio.com&gt;  
hostmaster@&lt;o-seu-nome-de-domínio.com&gt;  
postmaster@&lt;o-seu-nome-de-domínio.com&gt;  

Deverá receber um e-mail passados alguns minutos, semelhante ao seguinte exemplo, que lhe pede para aprovar o pedido. Se estiver a utilizar um filtro de spam, adicione admin@digicert.com à sua lista de admissões. Se não receber um e-mail passadas 24 horas, contacte o suporte da Microsoft.

Quando seleciona o link de aprovação, é direcionado para um formulário de aprovação online. Siga as instruções do formulário; tem duas opções de verificação:

- Pode aprovar todas as encomendas futuras feitas através da mesma conta para o mesmo domínio de raiz; por exemplo, contoso.com. Esta abordagem é recomendada se pretender adicionar mais domínios personalizados para o mesmo domínio raiz.

- Pode aprovar apenas o nome de anfitrião específico utilizado neste pedido. É necessária uma aprovação extra para pedidos posteriores.

Após a aprovação, a DigiCert conclui a criação do certificado para o seu nome de domínio personalizado. O certificado é válido por um ano e será autoidentificado antes de expirar.

## <a name="wait-for-propagation"></a>Aguardar pela propagação

Após a validação do nome de domínio, a ativação da funcionalidade HTTPS no domínio personalizado pode demorar entre 6 a 8 horas. Quando o processo estiver concluído, o estado do HTTPS personalizado no portal do Azure é definido como **Ativado** e os quatro passos da operação na caixa de diálogo de domínio personalizado são marcados como concluídos. O seu domínio personalizado está agora pronto para utilizar o HTTPS.

### <a name="operation-progress"></a>Progresso da operação

A tabela seguinte mostra o progresso da operação que ocorre quando ativa o HTTPS. Depois de ativar o HTTPS, aparecem quatro passos da operação na caixa de diálogo do domínio personalizado. À medida que cada passo se torna ativo, mais detalhes subpassam aparecem sob o passo à medida que progride. Nem todos estes subpassos vão ocorrer. Depois da conclusão bem-sucedida de um passo, aparece uma marca de verificação verde junto ao mesmo. 

| Passo da operação | Detalhes do subpasso da operação | 
| --- | --- |
| 1 Submeter o pedido | Submeter o pedido |
| | O seu pedido HTTPS está a ser submetido. |
| | O seu pedido HTTPS foi submetido com êxito. |
| 2 Validação do domínio | O domínio é automaticamente validado se for CNAME mapeado para o anfitrião por defeito .azurefd.net frontend da porta frontal. Caso contrário, será enviado um pedido de verificação para o e-mail indicado nas informações de registo do seu domínio (registo WHOIS). Verifique o domínio assim que possível. |
| | A propriedade do domínio foi validada com êxito. |
| | O pedido de validação da propriedade do domínio expirou (é provável que o cliente não tenha respondido em seis dias). HTTPS não será ativado no seu domínio. * |
| | O cliente rejeitou o pedido de validação da propriedade do domínio. HTTPS não será ativado no seu domínio. * |
| 3 Aprovisionamento do certificado | A autoridade de certificação está a emitir o certificado necessário para ativar o HTTPS no seu domínio. |
| | O certificado foi emitido e está a ser implementado no Front Door. Este processo pode demorar até uma hora. |
| | O certificado foi implementado com êxito no Front Door. |
| 4 Concluído | O HTTPS foi ativado com êxito no seu domínio. |

\* Esta mensagem não aparece, a menos que tenha ocorrido um erro. 

Se ocorrer um erro antes de o pedido ser submetido, será apresentada a seguinte mensagem de erro:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

1. *Quem é o fornecedor do certificado e que tipo de certificado é utilizado?*

    Um certificado dedicado/individual, fornecido pela Digicert, é utilizado para o seu domínio personalizado. 

2. *Utiliza TLS/SSL baseado em IP ou em SNI?*

    A porta frontal Azure utiliza SNI TLS/SSL.

3. *E se não receber o e-mail de verificação do domínio da DigiCert?*

    Se tiver uma entrada CNAME para o seu domínio personalizado que aponta diretamente para o seu nome de anfitrião do ponto final (e não está a usar o nome de subdomínio afdverify), não receberá um e-mail de verificação de domínio. A validação ocorre automaticamente. Caso contrário, se não tiver uma entrada CNAME e não tiver recebido um e-mail passadas 24 horas, contacte o suporte da Microsoft.

4. *A utilização de um certificado SAN é mais insegura do que um certificado dedicado?*
    
    Os certificados SAN seguem as mesmas normas de encriptação e segurança dos certificados dedicados. Todos os certificados TLS/SSL emitidos utilizam SHA-256 para uma segurança reforçada do servidor.

5. *Preciso de um registo Autorização de Autoridade de Certificação junto do meu fornecedor de DNS?*

    Não, um registo de autorização da Autoridade de Certificados não é atualmente necessário. No entanto, se tiver um, o mesmo tem de incluir a DigiCert como AC válida.

## <a name="clean-up-resources"></a>Limpar os recursos

Nos passos anteriores, ativou o protocolo HTTPS no seu domínio personalizado. Se já não pretender utilizar o seu domínio personalizado com HTTPS, pode desativar HTTPS fazendo os passos de teses:

### <a name="disable-the-https-feature"></a>Desativar a funcionalidade HTTPS 

1. No [portal Azure,](https://portal.azure.com)navegue pela configuração **da porta frontal Azure.**

2. Na lista de anfitriões frontend, selecione o domínio personalizado para o qual pretende desativar HTTPS.

3. Clique em **Desativado** para desativar o HTTPS e, em seguida, clique em **Guardar**.

### <a name="wait-for-propagation"></a>Aguardar pela propagação

A desativação da funcionalidade HTTPS no domínio personalizado pode demorar entre 6 a 8 horas a ter efeito. Quando o processo estiver concluído, o estado HTTPS personalizado no portal Azure é definido para **Desativado** e os três passos de funcionamento no diálogo de domínio personalizado são marcados como completos. O domínio personalizado já não pode utilizar o HTTPS.

#### <a name="operation-progress"></a>Progresso da operação

A tabela seguinte mostra o progresso da operação que ocorre quando desativa o HTTPS. Depois de desativar o HTTPS, aparecem três passos da operação na caixa de diálogo Domínio personalizado. À medida que cada passo se torna ativo, mais detalhes aparecem sob o passo. Depois da conclusão bem-sucedida de um passo, aparece uma marca de verificação verde junto ao mesmo. 

| Progresso da operação | Detalhes da operação | 
| --- | --- |
| 1 Submeter o pedido | Submeter o pedido |
| 2 Desaprovisionamento do certificado | Eliminar o certificado |
| 3 Concluído | Certificado eliminado |

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

* Faça o upload de um certificado para o Key Vault.
* Validar um domínio.
* Ativar HTTPS para o seu domínio personalizado.

Para aprender a configurar uma política de geo-filtragem para a sua Porta da Frente, continue até ao próximo tutorial.

> [!div class="nextstepaction"]
> [Estabeleça uma política de geo-filtragem](front-door-geo-filtering.md)
