---
title: Tutorial - Configurar o HTTPS num domínio personalizado da CDN do Azure | Microsoft Docs
description: Neste tutorial, vai aprender a ativar e desativar o HTTPS no seu domínio personalizado do ponto final da CDN do Azure.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/1/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 12cf4b029424bbbdb7449e6e1d04684ed485ef97
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779021"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Tutorial: Configurar o HTTPS num domínio personalizado da CDN do Azure

Este tutorial mostra como ativar o protocolo HTTPS num domínio personalizado associado a um ponto final da CDN do Azure. A utilização do protocolo HTTPS no seu domínio personalizado (por exemplo, https:\//www.contoso.com) garante que os seus dados confidenciais são entregues em segurança através de encriptação TLS/SSL quando são enviados pela Internet. Quando o browser está ligado a um site por HTTPS, valida o certificado de segurança do site e verifica que este é emitido por uma autoridade de certificação legítima. Este processo oferece segurança e protege as suas aplicações Web de ataques.

A CDN do Azure suporta o HTTPS num nome de anfitrião do ponto final da CDN, por predefinição. Por exemplo, se criar um ponto final da CDN (por exemplo, https:\//contoso.azureedge.net), o HTTPS é ativado automaticamente.  

Alguns dos principais atributos da funcionalidade HTTPS personalizada são:

- Sem custos adicionais: não há custos para a aquisição ou renovação de certificados nem para o tráfego HTTPS. Só paga pelos GB de saída da CDN.

- Ativação simples: está disponível no [portal do Azure](https://portal.azure.com) o aprovisionamento de um clique. Também pode utilizar a API REST ou outras ferramentas de programador para ativar a funcionalidade.

- Está disponível a gestão de certificados completa: todas as atividades de aprovisionamento e gestão de certificados são feitas por si. Os certificados são aprovisionados e renovados automaticamente antes de expirarem, o que elimina os riscos de interrupção do serviço devido à expiração dos mesmos.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Ativar o protocolo HTTPS no domínio personalizado.
> - Utilizar um certificado gerido pela CDN 
> - Utilize o seu próprio certificado
> - Validar o domínio
> - Desativar o protocolo HTTPS no domínio personalizado

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Antes de concluir os passos neste tutorial, tem primeiro de criar um perfil da CDN e, pelo menos, um ponto final da CDN. Para obter mais informações, veja [Início Rápido: Criar um perfil e um ponto final da CDN do Azure](cdn-create-new-endpoint.md).

Além disso, tem de associar um domínio personalizado da CDN do Azure no ponto final da CDN. Para obter mais informações, consulte [Tutorial: Adicione um domínio personalizado ao seu ponto final Azure CDN](cdn-map-content-to-custom-domain.md).

> [!IMPORTANT]
> Os certificados geridos pela CDN não estão disponíveis para domínios raiz ou ápice. Se o seu domínio personalizado Azure CDN for um domínio raiz ou ápice, deve utilizar a função de certificado Bring. 
>

---

## <a name="tlsssl-certificates"></a>Certificados TLS/SSL
Para ativar o protocolo HTTPS para a entrega segura de conteúdos num domínio personalizado Azure CDN, deve utilizar um certificado TLS/SSL. Pode optar por utilizar um certificado gerido pela CDN do Azure ou utilizar o seu próprio certificado.


# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Opção 1 (predefinição): ativar HTTPS com um certificado gerido pela CDN](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Quando utiliza um certificado gerida pela CDN, a funcionalidade HTTPS pode ser ativada com apenas alguns cliques. A CDN do Azure processa inteiramente as tarefas de gestão do certificado, tais como o aprovisionamento e a renovação. Depois de ativar a funcionalidade, o processo é iniciado imediatamente. Se o domínio personalizado já estiver mapeado para o ponto final da CDN, não será necessária mais nenhuma ação. A CDN do Azure vai processar os passos e concluir o pedido automaticamente. No entanto, se o domínio personalizado estiver mapeado noutro local, terá de utilizar o e-mail para validar a propriedade do domínio.

Para ativar o HTTPS num domínio personalizado, siga estes passos:

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar um certificado gerido pelo seu Azure CDN. Procure e selecione **perfis CDN** . 

2. Escolha o **seu Azure CDN Standard da Microsoft** , **Azure CDN Standard da Akamai** , **Azure CDN Standard da Verizon** ou **Azure CDN Premium do** perfil Verizon.

3. Na lista de pontos finais da CDN, selecione o ponto final que contém o seu domínio personalizado.

    ![Lista de pontos finais](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    É apresentada a página **Ponto final** .

4. Na lista de domínios personalizados, selecione o domínio personalizado no qual pretende ativar o HTTPS.

    ![A screenshot mostra a página de domínio personalizado com a opção de usar o meu próprio certificado.](./media/cdn-custom-ssl/cdn-custom-domain.png)

    É apresentada a página **Domínio personalizado** .

5. Em Tipo de gestão de certificado, selecione **CDN gerida** .

6. Selecione **Ativar** para ativar o HTTPS.

    ![Estado do HTTPS do domínio personalizado](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. Avance para [Validar o domínio](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificate"></a>[Opção 2: ativar a funcionalidade HTTPS com o seu próprio certificado](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Esta opção está disponível apenas com **Azure CDN da Microsoft** e **Azure CDN a partir de** perfis Verizon. 
>
 
Pode utilizar o seu próprio certificado para ativar a funcionalidade HTTPS. Este processo é efetuado através de uma integração com o Azure Key Vault, o que lhe permite armazenar os certificados de forma segura. A CDN do Azure utiliza este mecanismo seguro para obter o certificado e requer alguns passos adicionais. Ao criar o seu certificado TLS/SSL, deve criá-lo com uma autoridade de certificados permitida (CA). Caso contrário, se utilizar uma AC não permitida, o pedido será rejeitado. Para obter uma lista de CAs [autorizados, consulte as autoridades de certificados autorizados para permitir https personalizados em Azure CDN](cdn-troubleshoot-allowed-ca.md). Para **a Azure CDN da Verizon,** qualquer CA válido será aceite. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Prepare a conta e o certificado do Azure Key Vault
 
1. Azure Key Vault: tem de ter uma conta do Azure Key Vault em execução na mesma subscrição que o perfil da CDN do Azure e os pontos finais da CDN para os quais pretende ativar HTTPS personalizado. Se não tiver uma, crie uma conta do Azure Key Vault.
 
2. Certificados do Azure Key Vault: se já tiver um certificado, poderá carregá-lo diretamente na conta do Azure Key Vault ou pode criar um novo diretamente através do Azure Key Vault a partir de uma das CAs do parceiro com o qual o Azure Key Vault está integrado. 

### <a name="register-azure-cdn"></a>Registar a CDN do Azure

Registe a CDN do Azure como uma aplicação no Azure Active Directory através do PowerShell.

1. Se necessário, instale [o Azure PowerShell](/powershell/azure/install-az-ps) na sua máquina local.

2. No PowerShell, execute o seguinte comando:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`

    ![Registar a CDN do Azure no PowerShell](./media/cdn-custom-ssl/cdn-register-powershell.png)
              

### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Conceda acesso da CDN do Azure ao seu cofre de chaves
 
Dê permissão à CDN do Azure para aceder aos certificados (segredos) na conta do Azure Key Vault.

1. Na sua conta do cofre de chaves, em DEFINIÇÕES, selecione **Políticas de acesso** e **Adicionar nova** para criar uma nova política.

    ![Criar uma nova política de acesso](./media/cdn-custom-ssl/cdn-new-access-policy.png)

2. Em **Selecionar principal** , procure **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** e escolha **Microsoft.Azure.Cdn** . Clique em **Selecionar** .

    ![Definições da política de acesso](./media/cdn-custom-ssl/cdn-access-policy-settings.png)

3. Selecione **permissões de Certificado** e, em seguida, selecione as caixas de verificação para **Obter** e **Lista** para permitir que a CDN execute estas permissões para obter e listar os certificados.

4. Selecione **permissões Secretas** e, em seguida, selecione as caixas de verificação para **Obter** e **Lista** para permitir que a CDN execute estas permissões para obter e listar os Segredos.

5. Selecione **OK** . 

    A CDN do Azure pode agora aceder a este cofre de chaves e aos certificados (segredos) que estão armazenados neste cofre de chaves.
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Selecione o certificado que vai ser implementado pela CDN do Azure
 
1. Regresse ao portal da CDN do Azure e selecione o perfil e o ponto final da CDN para os quais pretende ativar HTTPS personalizado. 

2. Na lista de domínios personalizados, selecione o domínio personalizado no qual pretende ativar o HTTPS.

    É apresentada a página **Domínio personalizado** .

3. Em Tipo de gestão de certificado, selecione **Utilizar o meu próprio certificado** . 

    ![Configurar o certificado](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Selecione um cofre de chaves, o certificado (segredo) e a versão do certificado.

    A CDN do Azure lista as seguintes informações: 
    - As contas do cofre de chaves do ID da sua subscrição. 
    - Os certificados (segredos) no cofre de chaves selecionado. 
    - As versões dos certificados disponíveis. 
 
5. Selecione **Ativar** para ativar o HTTPS.
  
6. Quando utiliza o seu próprio certificado, a validação de domínio não é necessária. Avance para [Aguardar pela propagação](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Validar o domínio

Se já tiver um domínio personalizado em utilização que esteja mapeado para o ponto final personalizado com um registo CNAME ou estiver a utilizar o seu próprio certificado, avance para  
[O domínio personalizado está mapeado para o ponto final da CDN](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). Caso contrário, se a entrada do registo CNAME para o ponto final já não existir ou contiver o subdomínio cdnverify, avance para [O domínio personalizado não está mapeado para o ponto final da CDN](#custom-domain-is-not-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>O domínio personalizado está mapeado para o ponto final da CDN por um registo CNAME

Quando adicionou um domínio personalizado ao ponto final, criou um registo CNAME na tabela de DNS da sua entidade de registo de domínios para mapeá-lo para o nome de anfitrião do ponto final da CDN. Se esse registo CNAME ainda existir e não contiver o subdomínio cdnverify, a CA do DigiCert utiliza-o para validar automaticamente a propriedade do seu domínio personalizado. 

Se utilizar o seu próprio certificado, a validação de domínio não é necessária.

O registo CNAME deve estar no seguinte formato, em que *Nome* é o nome do seu domínio personalizado e *Valor* é o nome de anfitrião do ponto final da CDN:

| Nome            | Tipo  | Valor                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

Para obter mais informações sobre os registos CNAME, veja [Criar o registo DNS CNAME](./cdn-map-content-to-custom-domain.md).

Se o registo CNAME estiver no formato correto, DigiCert verifica o seu nome de domínio personalizado automaticamente e cria um certificado dedicado para o seu nome de domínio. DigitCert não lhe enviará um e-mail de verificação e não terá de aprovar o seu próprio pedido. O certificado é válido durante um ano e será renovado automaticamente antes de expirar. Avance para [Aguardar pela propagação](#wait-for-propagation). 

A validação automática normalmente demora algumas horas. Se não vir o seu domínio validado em 24 horas, abra um bilhete de apoio.

>[!NOTE]
>Se tiver um registo Autorização de Autoridade de Certificação (CAA) com o seu fornecedor de DNS, tem de incluir DigiCert como AC válida. O registo CAA permite aos proprietários de domínios especificar junto dos respetivos fornecedores de DNS que ACs têm autorização para emitir certificados para os seus domínios. Se uma AC receber um pedido de certificado para um domínio que tenha um registo CAA e essa AC não estiver listada como emissora autorizada, estará proibida de emitir o certificado para esse domínio ou subdomínio. Para obter informações sobre como gerir registos CAA, veja [Manage CAA records](https://support.dnsimple.com/articles/manage-caa-record/) (Gerir registos CAA). Para obter uma ferramenta de registo CAA, veja [CAA Record Helper](https://sslmate.com/caa/) (Ajuda para Registos CAA).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>O domínio personalizado não está mapeado para o ponto final da CDN

>[!NOTE]
>Se estiver a utilizar **o Azure CDN da Akamai,** deve ser configurado o seguinte CNAME para permitir a validação automatizada de domínio. "_acme-desafio. &lt; nome anfitrião de domínio personalizado &gt; -> CNAME -> &lt; nome de anfitrião de domínio personalizado &gt; .ak-acme-challenge.azureedge.net"

Se a entrada de registo CNAME contiver o subdomínio cdnverify, siga o resto das instruções neste passo.

A DigiCert envia um e-mail de verificação para os seguintes endereços de e-mail. Verifique se pode aprovar diretamente a partir de um dos seguintes endereços:

admin@&lt;o-seu-nome-de-domínio.com&gt;  
administrador@&lt;o-seu-nome-de-domínio.com&gt;  
webmaster@&lt;o-seu-nome-de-domínio.com&gt;  
hostmaster@&lt;o-seu-nome-de-domínio.com&gt;  
postmaster@&lt;o-seu-nome-de-domínio.com&gt;  

Deverá receber um e-mail passados alguns minutos, semelhante ao seguinte exemplo, que lhe pede para aprovar o pedido. Se estiver a utilizar um filtro de spam, adicione verification@digicert.com a sua lista de admissões. Se não receber um e-mail passadas 24 horas, contacte o suporte da Microsoft.
    
![E-mail de validação do domínio](./media/cdn-custom-ssl/domain-validation-email.png)

Quando clica na ligação de aprovação, é direcionado para o seguinte formulário de aprovação online: 
    
![Formulário de validação do domínio](./media/cdn-custom-ssl/domain-validation-form.png)

Siga as instruções do formulário; tem duas opções de verificação:

- Pode aprovar todas as encomendas futuras feitas através da mesma conta para o mesmo domínio de raiz; por exemplo, contoso.com. Esta abordagem é recomendada se planear adicionar mais domínios personalizados para o mesmo domínio de raiz.

- Pode aprovar apenas o nome de anfitrião específico utilizado neste pedido. É necessária aprovação adicional para pedidos subsequentes.

Após a aprovação, a DigiCert conclui a criação do certificado para o seu nome de domínio personalizado. O certificado é válido durante um ano e será renovado automaticamente antes de expirar.

## <a name="wait-for-propagation"></a>Aguardar pela propagação

Após a validação do nome de domínio, a ativação da funcionalidade HTTPS no domínio personalizado pode demorar entre 6 a 8 horas. Quando o processo estiver concluído, o estado do HTTPS personalizado no portal do Azure é definido como **Ativado** e os quatro passos da operação na caixa de diálogo de domínio personalizado são marcados como concluídos. O seu domínio personalizado está agora pronto para utilizar o HTTPS.

![Caixa de diálogo Ativar HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Progresso da operação

A tabela seguinte mostra o progresso da operação que ocorre quando ativa o HTTPS. Depois de ativar o HTTPS, aparecem quatro passos da operação na caixa de diálogo do domínio personalizado. À medida que cada passo é ativado, aparecem detalhes de subpassos adicionais abaixo dos passos. Nem todos estes subpassos vão ocorrer. Depois da conclusão bem-sucedida de um passo, aparece uma marca de verificação verde junto ao mesmo. 

| Passo da operação | Detalhes do subpasso da operação | 
| --- | --- |
| 1 Submeter o pedido | Submeter o pedido |
| | O seu pedido HTTPS está a ser submetido. |
| | O seu pedido HTTPS foi submetido com êxito. |
| 2 Validação do domínio | O domínio é validado automaticamente se estiver mapeado para o ponto final da CDN através de CNAME. Caso contrário, será enviado um pedido de verificação para o e-mail indicado nas informações de registo do seu domínio (registo WHOIS). Verifique o domínio assim que possível. |
| | A propriedade do domínio foi validada com êxito. |
| | O pedido de validação da propriedade do domínio expirou (é provável que o cliente não tenha respondido em seis dias). O HTTPS não será ativado no seu domínio. * |
| | O cliente rejeitou o pedido de validação da propriedade do domínio. O HTTPS não será ativado no seu domínio. * |
| 3 Aprovisionamento do certificado | A autoridade de certificação está a emitir o certificado necessário para ativar o HTTPS no seu domínio. |
| | O certificado foi emitido e está a ser implementado na rede CDN. Este processo poderá demorar até seis horas. |
| | O certificado foi implementado com êxito na rede CDN. |
| 4 Concluído | O HTTPS foi ativado com êxito no seu domínio. |

\* Esta mensagem não aparece, a menos que tenha ocorrido um erro. 

Se ocorrer um erro antes de o pedido ser submetido, será apresentada a seguinte mensagem de erro:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Limpar recursos - desativar HTTPS

Nos passos anteriores, ativou o protocolo HTTPS no seu domínio personalizado. Se já não pretender utilizar o seu domínio personalizado com o HTTPS, pode seguir os passos abaixo para desativá-lo:

### <a name="disable-the-https-feature"></a>Desativar a funcionalidade HTTPS 

1. No [portal Azure,](https://portal.azure.com)procure e selecione **perfis CDN** . 

2. Escolha o **Seu Azure CDN Standard da Microsoft** , **Azure CDN Standard da Verizon** , ou **Azure CDN Premium do** perfil Verizon.

3. Na lista de pontos finais, escolha o ponto final que contém o seu domínio personalizado.

4. Escolha o domínio personalizado para o qual pretende desativar HTTPS.

    ![Lista de domínios personalizados](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. Escolha **desligar** para desativar HTTPS e, em seguida, selecione **Aplicar** .

    ![Caixa de diálogo de HTTPS personalizado](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Aguardar pela propagação

A desativação da funcionalidade HTTPS no domínio personalizado pode demorar entre 6 a 8 horas a ter efeito. Quando o processo estiver concluído, o estado do HTTPS personalizado no portal do Azure é definido como **Desativado** e os três passos da operação na caixa de diálogo de domínio personalizado são marcados como concluídos. O domínio personalizado já não pode utilizar o HTTPS.

![Caixa de diálogo Desativar HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Progresso da operação

A tabela seguinte mostra o progresso da operação que ocorre quando desativa o HTTPS. Depois de desativar o HTTPS, aparecem três passos da operação na caixa de diálogo Domínio personalizado. À medida que cada passo é ativado, aparecem detalhes adicionais abaixo dos mesmos. Depois da conclusão bem-sucedida de um passo, aparece uma marca de verificação verde junto ao mesmo. 

| Progresso da operação | Detalhes da operação | 
| --- | --- |
| 1 Submeter o pedido | Submeter o pedido |
| 2 Desaprovisionamento do certificado | Eliminar o certificado |
| 3 Concluído | Certificado eliminado |

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

1. *Quem é o fornecedor do certificado e que tipo de certificado é utilizado?*

    Para a **CDN do Azure da Verizon** e a **CDN do Azure da Microsoft** , é utilizado um certificado dedicado/único dado pela Digicert para o seu domínio personalizado. 

2. *Utiliza TLS/SSL baseado em IP ou em SNI?*

    A **CDN do Azure da Verizon** e a **CDN do Azure Standard da Microsoft** utilizam SNI TLS/SSL.

3. *E se não receber o e-mail de verificação do domínio da DigiCert?*

    Se tiver uma entrada CNAME para o seu domínio personalizado que aponte diretamente para o nome de anfitrião do seu ponto final (e não estiver a utilizar o nome de subdomínio cdnverify), não receberá um e-mail de verificação do domínio. A validação ocorre automaticamente. Caso contrário, se não tiver uma entrada CNAME e não tiver recebido um e-mail passadas 24 horas, contacte o suporte da Microsoft.

4. *A utilização de um certificado SAN é mais insegura do que um certificado dedicado?*
    
    Os certificados SAN seguem as mesmas normas de encriptação e segurança dos certificados dedicados. Todos os certificados TLS/SSL emitidos utilizam SHA-256 para uma segurança reforçada do servidor.

5. *Preciso de um registo Autorização de Autoridade de Certificação junto do meu fornecedor de DNS?*

    Não, atualmente os registos Autorização de Autoridade de Certificação não são necessários. No entanto, se tiver um, o mesmo tem de incluir a DigiCert como AC válida.

6. *Em 20 de junho de 2018, a Azure CDN da Verizon começou a usar um certificado dedicado com SNI TLS/SSL por padrão. O que acontece aos meus domínios personalizados existentes utilizando o certificado Subject Alternative Names (SAN) e o TLS/SSL baseado em IP?*

    Os domínios existentes serão gradualmente migrados para um único certificado nos meses seguintes se a Microsoft analisar que apenas os pedidos de cliente do SNI são realizados na sua aplicação. Se a Microsoft detetar alguns pedidos de cliente não SNI feitos na sua aplicação, os seus domínios permanecerão no certificado SAN com TLS/SSL baseado em IP. De qualquer forma, não haverá interrupções no seu serviço ou suporte aos seus pedidos de cliente, independentemente se esses pedidos são SNI ou não.

7. *Como funcionam as renovações do cert com o Seu Próprio Certificado?*

    Para garantir que um certificado mais recente é implantado na infraestrutura PoP, basta enviar o seu novo certificado para Azure KeyVault e, em seguida, nas definições de TLS no Azure CDN, escolha a versão mais recente do certificado e ature save. O Azure CDN irá então propagar o seu novo certificado atualizado. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> - Ativar o protocolo HTTPS no domínio personalizado.
> - Utilizar um certificado gerido pela CDN 
> - Utilize o seu próprio certificado
> - Valide o domínio
> - Desativar o protocolo HTTPS no domínio personalizado

Avance para o próximo tutorial para saber como configurar a colocação em cache no seu ponto final da CDN.

> [!div class="nextstepaction"]
> [Tutorial: Definir regras de colocação em cache da CDN do Azure](cdn-caching-rules-tutorial.md)