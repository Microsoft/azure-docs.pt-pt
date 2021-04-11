---
title: 'Tutorial: Configurar o HTTPS num domínio personalizado da CDN do Azure'
description: Neste tutorial, vai aprender a ativar e desativar o HTTPS no seu domínio personalizado do ponto final da CDN do Azure.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 6f77bac93b7bb5e3319409c01e328c73cd08a9a0
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058957"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Tutorial: Configurar o HTTPS num domínio personalizado da CDN do Azure

Este tutorial mostra como ativar o protocolo HTTPS num domínio personalizado associado a um ponto final da CDN do Azure. 

O protocolo HTTPS no seu domínio personalizado (por exemplo, https: \/ /www.contoso.com), garante que os seus dados sensíveis são entregues de forma segura através de TLS/SSL. Quando o seu navegador está conectado via HTTPS, o navegador valida o certificado do site. O navegador verifica que é emitido por uma autoridade legítima de certificado. Este processo oferece segurança e protege as suas aplicações Web de ataques.

A CDN do Azure suporta o HTTPS num nome de anfitrião do ponto final da CDN, por predefinição. Por exemplo, se criar um ponto final da CDN (por exemplo, https:\//contoso.azureedge.net), o HTTPS é ativado automaticamente.  

Alguns dos principais atributos da funcionalidade HTTPS personalizada são:

- Sem custos adicionais: Não há custos para aquisição ou renovação de certificados e nenhum custo extra para o tráfego HTTPS. Só paga pelos GB de saída da CDN.

- Ativação simples: está disponível no [portal do Azure](https://portal.azure.com) o aprovisionamento de um clique. Também pode utilizar a API REST ou outras ferramentas de programador para ativar a funcionalidade.

- A gestão completa dos certificados está disponível: 
    * Todos os contratos de certificação e gestão são tratados por si. 
    * Os certificados são automaticamente a provisionados e renovados antes do termo.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Ativar o protocolo HTTPS no domínio personalizado.
> - Utilizar um certificado gerido pela CDN 
> - Utilize o seu próprio certificado
> - Validar o domínio
> - Desativar o protocolo HTTPS no domínio personalizado

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Antes de completar os passos neste tutorial, crie um perfil CDN e pelo menos um ponto final CDN. Para obter mais informações, veja [Início Rápido: Criar um perfil e um ponto final da CDN do Azure](cdn-create-new-endpoint.md).

Associar um domínio personalizado Azure CDN no seu ponto final CDN. Para obter mais informações, consulte [Tutorial: Adicione um domínio personalizado ao seu ponto final Azure CDN](cdn-map-content-to-custom-domain.md).

> [!IMPORTANT]
> Os certificados geridos pela CDN não estão disponíveis para domínios raiz ou ápice. Se o seu domínio personalizado Azure CDN for um domínio raiz ou ápice, deve utilizar a função de certificado Bring. 
>

---

## <a name="tlsssl-certificates"></a>Certificados TLS/SSL

Para ativar HTTPS num domínio personalizado Azure CDN, utilize um certificado TLS/SSL. Opta por utilizar um certificado gerido pela Azure CDN ou utilizar o seu certificado.

# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Opção 1 (predefinição): ativar HTTPS com um certificado gerido pela CDN](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

A Azure CDN trata de tarefas de gestão de certificados, tais como aquisição e renovação. Depois de ativar a funcionalidade, o processo é iniciado imediatamente. 

Se o domínio personalizado já estiver mapeado para o ponto final do CDN, não são necessárias mais medidas. A CDN do Azure vai processar os passos e concluir o pedido automaticamente.

Se o seu domínio personalizado estiver mapeado noutro local, utilize o e-mail para validar a propriedade do seu domínio.

Para ativar o HTTPS num domínio personalizado, siga estes passos:

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar um certificado gerido pelo seu Azure CDN. Procure e selecione **perfis CDN**. 

2. Escolha o seu perfil:
    * **Azure CDN Standard da Microsoft**
    * **Azure CDN Standard da Akamai**
    * **Azure CDN Standard de Verizon**
    * **Azure CDN Premium de Verizon**

3. Na lista de pontos finais da CDN, selecione o ponto final que contém o seu domínio personalizado.

    ![Lista de pontos finais](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    É apresentada a página **Ponto final**.

4. Na lista de domínios personalizados, selecione o domínio personalizado no qual pretende ativar o HTTPS.

    ![A screenshot mostra a página de domínio personalizado com a opção de usar o meu próprio certificado.](./media/cdn-custom-ssl/cdn-custom-domain.png)

    É apresentada a página **Domínio personalizado**.

5. Em Tipo de gestão de certificado, selecione **CDN gerida**.

6. Selecione **Ativar** para ativar o HTTPS.

    ![Estado do HTTPS do domínio personalizado](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. Continue a [validar o domínio.](#validate-the-domain)


# <a name="option-2-enable-https-with-your-own-certificate"></a>[Opção 2: ativar a funcionalidade HTTPS com o seu próprio certificado](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Esta opção está disponível apenas com **Azure CDN da Microsoft** e **Azure CDN a partir de** perfis Verizon. 
>
 
Pode utilizar o seu próprio certificado para ativar a funcionalidade HTTPS. Este processo é efetuado através de uma integração com o Azure Key Vault, o que lhe permite armazenar os certificados de forma segura. O Azure CDN utiliza este mecanismo seguro para obter o seu certificado e requer alguns passos extras. Ao criar o seu certificado TLS/SSL, deve criá-lo com uma autoridade de certificados permitida (CA). Caso contrário, se utilizar uma AC não permitida, o pedido será rejeitado. Para obter uma lista de ACs permitidas, veja [Autoridades de certificação permitidas para ativar o HTTPS personalizado na CDN do Azure](cdn-troubleshoot-allowed-ca.md). Para **a Azure CDN da Verizon,** qualquer CA válido será aceite. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Prepare a conta e o certificado do Azure Key Vault
 
1. Azure Key Vault: tem de ter uma conta do Azure Key Vault em execução na mesma subscrição que o perfil da CDN do Azure e os pontos finais da CDN para os quais pretende ativar HTTPS personalizado. Se não tiver uma, crie uma conta do Azure Key Vault.
 
2. Certificados Azure Key Vault: Se tiver um certificado, faça o upload diretamente para a sua conta Azure Key Vault. Se não tiver um certificado, crie um novo certificado diretamente através do Cofre da Chave Azure.

### <a name="register-azure-cdn"></a>Registar a CDN do Azure

Registe a CDN do Azure como uma aplicação no Azure Active Directory através do PowerShell.

1. Se necessário, instale [o Azure PowerShell](/powershell/azure/install-az-ps) na sua máquina local.

2. No PowerShell, execute o seguinte comando:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`
    > [!NOTE]
    > **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** é o principal de serviço da **Microsoft.AzureFrontDoor-Cdn**.

    ```bash  
    New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"

    Secret                : 
    ServicePrincipalNames : {205478c0-bd83-4e1b-a9d6-db63a3e1e1c8, 
                                https://microsoft.onmicrosoft.com/033ce1c9-f832-4658-b024-ef1cbea108b8}
    ApplicationId         : 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8
    ObjectType            : ServicePrincipal
    DisplayName           : Microsoft.AzureFrontDoor-Cdn
    Id                    : c87be08f-686a-4d9f-8ef8-64707dbd413e
    Type                  :
    ```
### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Conceda acesso da CDN do Azure ao seu cofre de chaves
 
Dê permissão à CDN do Azure para aceder aos certificados (segredos) na conta do Azure Key Vault.

1. No seu cofre chave na secção **Definições,** selecione **as políticas de acesso**. No painel direito, selecione **+ Adicionar Política de Acesso**:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-new-access-policy.png" alt-text="Criar política de acesso a teclados para a CDN" border="true":::

2. Na página **de política de acesso adicionar,** selecione Nenhum **selecionado** ao lado de **Select principal**. Na página **principal,** insira **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**. Selecione **Microsoft.AzureFrontdoor-Cdn**.  Escolha **Selecione:**

2. In **Select principal**, search for **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**, escolha **Microsoft.AzureFrontDoor-Cdn**. Escolha **Selecionar**.

    :::image type="content" source="./media/cdn-custom-ssl/cdn-access-policy-settings.png" alt-text="Selecione o principal de serviço da Azure CDN" border="true":::
    
3. Selecione **permissões de certificado**. Selecione as caixas de verificação para **Obter** e **Lista** para permitir permissões de CDN para obter e listar os certificados.

4. Selecione **permissões secretas.** Selecione as caixas de verificação para **Obter** e **Lista** para permitir permissões de CDN para obter e listar os segredos:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-vault-permissions.png" alt-text="Selecione permissões para CDN para keyvault" border="true":::

5. Selecione **Adicionar**. 

> [!NOTE]
> A CDN do Azure pode agora aceder a este cofre de chaves e aos certificados (segredos) que estão armazenados neste cofre de chaves. Qualquer instância CDN criada nesta subscrição terá acesso aos certificados neste cofre chave. 

 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Selecione o certificado que vai ser implementado pela CDN do Azure
 
1. Regresse ao portal da CDN do Azure e selecione o perfil e o ponto final da CDN para os quais pretende ativar HTTPS personalizado. 

2. Na lista de domínios personalizados, selecione o domínio personalizado no qual pretende ativar o HTTPS.

    É apresentada a página **Domínio personalizado**.

3. Em Tipo de gestão de certificado, selecione **Utilizar o meu próprio certificado**. 

    :::image type="content" source="./media/cdn-custom-ssl/cdn-configure-your-certificate.png" alt-text="Screenshot de como configurar o certificado para o ponto final do CDN.":::

4. Selecione um cofre chave, certificado/segredo e versão Certificado/Segredo.

    A CDN do Azure lista as seguintes informações: 
    - As contas do cofre de chaves do ID da sua subscrição. 
    - Os certificados/segredos sob o cofre de chaves selecionado. 
    - O certificado/versões secretas disponíveis.
 
    > [!NOTE]
    > Para que o certificado seja automaticamente rodado para a versão mais recente quando uma versão mais recente do certificado estiver disponível no seu Cofre-Chave, por favor, desa um certificado/versão secreta para 'Mais recente'. Se for selecionada uma versão específica, terá de voltar a selecionar a nova versão manualmente para a rotação do certificado. Leva até 24 horas para que a nova versão do certificado/segredo seja implantada. 
   
5. Selecione **Ativar** para ativar o HTTPS.
  
6. Quando utiliza o certificado, a validação de domínio não é necessária. Continue a [aguardar a propagação.](#wait-for-propagation)

---

## <a name="validate-the-domain"></a>Validar o domínio

Se tiver um domínio personalizado em uso mapeado para o seu ponto final personalizado com um registo CNAME ou se estiver a utilizar o seu próprio certificado, continue a [ter o domínio personalizado mapeado para o seu ponto final CDN](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). 

Caso contrário, se a entrada de registo CNAME para o seu ponto final já não existir ou contiver o subdomínio cdnver, continue a [ter domínio personalizado não mapeado para o seu ponto final CDN](#custom-domain-isnt-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>O domínio personalizado está mapeado para o ponto final da CDN por um registo CNAME

Quando adicionou um domínio personalizado ao seu ponto final, criou um registo CNAME no registo de domínio DNS mapeado para o seu nome de anfitrião do ponto final CDN. 

Se esse registo CNAME ainda existir e não contiver o subdomínio cdnverify, o DigiCert CA utiliza-o para validar automaticamente a propriedade do seu domínio personalizado. 

Se estiver a usar o seu próprio certificado, a validação de domínio não é necessária.

O seu registo CNAME deve estar no seguinte formato:

* *Nome* é o seu nome de domínio personalizado.
* *Valor* é o seu nome de anfitrião do ponto final CDN.

| Nome            | Tipo  | Valor                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

Para obter mais informações sobre os registos CNAME, veja [Criar o registo DNS CNAME](./cdn-map-content-to-custom-domain.md).

Se o seu registo CNAME estiver no formato correto, a DigiCert verifica automaticamente o nome de domínio personalizado e cria um certificado para o seu domínio. DigitCert não lhe enviará um e-mail de verificação e não terá de aprovar o seu próprio pedido. O certificado é válido por um ano e será auto-autoriado antes de expirar. Continue a [aguardar a propagação.](#wait-for-propagation) 

A validação automática normalmente demora algumas horas. Se não vir o seu domínio validado em 24 horas, abra um bilhete de apoio.

>[!NOTE]
>Se tiver um registo Autorização de Autoridade de Certificação (CAA) com o seu fornecedor de DNS, tem de incluir DigiCert como AC válida. O registo CAA permite aos proprietários de domínios especificar junto dos respetivos fornecedores de DNS que ACs têm autorização para emitir certificados para os seus domínios. Se uma AC receber um pedido de certificado para um domínio que tenha um registo CAA e essa AC não estiver listada como emissora autorizada, estará proibida de emitir o certificado para esse domínio ou subdomínio. Para obter informações sobre como gerir registos CAA, veja [Manage CAA records](https://support.dnsimple.com/articles/manage-caa-record/) (Gerir registos CAA). Para obter uma ferramenta de registo CAA, veja [CAA Record Helper](https://sslmate.com/caa/) (Ajuda para Registos CAA).

### <a name="custom-domain-isnt-mapped-to-your-cdn-endpoint"></a>O domínio personalizado não está mapeado para o seu ponto final cdn

>[!NOTE]
>Se estiver a utilizar **o Azure CDN da Akamai,** deve ser configurado o seguinte CNAME para permitir a validação automatizada de domínio. "_acme-desafio. &lt; nome anfitrião de domínio personalizado &gt; -> CNAME -> &lt; nome de anfitrião de domínio personalizado &gt; .ak-acme-challenge.azureedge.net"

Se a entrada de registo CNAME contiver o subdomínio cdnverify, siga o resto das instruções neste passo.

A DigiCert envia um e-mail de verificação para os seguintes endereços de e-mail. Verifique se pode aprovar diretamente a partir de um dos seguintes endereços:

* **admin@your-domain-name.com** 
* **administrator@your-domain-name.com** 
* **webmaster@your-domain-name.com** 
* **hostmaster@your-domain-name.com**  
* **postmaster@your-domain-name.com**  

Deverá receber um e-mail em poucos minutos para que aprove o pedido. No caso de utilizar um filtro de spam, adicione verification@digicert.com à sua lista de admissões. Se não receber um e-mail passadas 24 horas, contacte o suporte da Microsoft.
    
![E-mail de validação do domínio](./media/cdn-custom-ssl/domain-validation-email.png)

Quando seleciona o link de aprovação, é direcionado para o seguinte formulário de aprovação online: 
    
![Formulário de validação do domínio](./media/cdn-custom-ssl/domain-validation-form.png)

Siga as instruções do formulário; tem duas opções de verificação:

- Pode aprovar todas as encomendas futuras feitas através da mesma conta para o mesmo domínio de raiz; por exemplo, contoso.com. Esta abordagem é recomendada se pretender adicionar outros domínios personalizados para o mesmo domínio raiz.

- Pode aprovar apenas o nome de anfitrião específico utilizado neste pedido. É necessária outra aprovação para pedidos posteriores.

Após a aprovação, a DigiCert conclui a criação do certificado para o seu nome de domínio personalizado. O certificado é válido por um ano e será autoidentificado antes de expirar.

## <a name="wait-for-propagation"></a>Aguardar pela propagação

Após a validação do nome de domínio, a ativação da funcionalidade HTTPS no domínio personalizado pode demorar entre 6 a 8 horas. Quando o processo estiver concluído, o estado HTTPS personalizado no portal Azure é alterado para **Ativado**. Os quatro passos de funcionamento no diálogo de domínio personalizado estão marcados como completos. O seu domínio personalizado está agora pronto para utilizar o HTTPS.

![Caixa de diálogo Ativar HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Progresso da operação

A tabela seguinte mostra o progresso da operação que ocorre quando ativa o HTTPS. Depois de ativar o HTTPS, aparecem quatro passos da operação na caixa de diálogo do domínio personalizado. À medida que cada passo se torna ativo, outros detalhes do subpass aparecem sob o passo à medida que progride. Nem todos estes subpassos vão ocorrer. Depois da conclusão bem-sucedida de um passo, aparece uma marca de verificação verde junto ao mesmo. 

| Passo da operação | Detalhes do subpasso da operação | 
| --- | --- |
| 1 Submeter o pedido | Submeter o pedido |
| | O seu pedido HTTPS está a ser submetido. |
| | O seu pedido HTTPS foi submetido com êxito. |
| 2 Validação do domínio | O domínio é automaticamente validado se for o CNAME mapeado para o ponto final do CDN. Caso contrário, será enviado um pedido de verificação para o e-mail indicado nas informações de registo do seu domínio (registo WHOIS).|
| | A propriedade do domínio foi validada com êxito. |
| | O pedido de validação da propriedade do domínio expirou (é provável que o cliente não tenha respondido em seis dias). HTTPS não será ativado no seu domínio. * |
| | O cliente rejeitou o pedido de validação da propriedade do domínio. HTTPS não será ativado no seu domínio. * |
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

Nesta secção, aprende a desativar HTTPS para o seu domínio personalizado.

### <a name="disable-the-https-feature"></a>Desativar a funcionalidade HTTPS 

1. No [portal Azure,](https://portal.azure.com)procure e selecione **perfis CDN**. 

2. Escolha o **Seu Azure CDN Standard da Microsoft**, **Azure CDN Standard da Verizon**, ou **Azure CDN Premium do** perfil Verizon.

3. Na lista de pontos finais, escolha o ponto final que contém o seu domínio personalizado.

4. Escolha o domínio personalizado para o qual pretende desativar HTTPS.

    ![Lista de domínios personalizados](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. Escolha **desligar** para desativar HTTPS e, em seguida, selecione **Aplicar**.

    ![Caixa de diálogo de HTTPS personalizado](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Aguardar pela propagação

A desativação da funcionalidade HTTPS no domínio personalizado pode demorar entre 6 a 8 horas a ter efeito. Quando o processo estiver concluído, o estado HTTPS personalizado no portal Azure é alterado para **Desativado**. Os três passos de funcionamento no diálogo de domínio personalizado estão marcados como completos. O domínio personalizado já não pode utilizar o HTTPS.

![Caixa de diálogo Desativar HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Progresso da operação

A tabela seguinte mostra o progresso da operação que ocorre quando desativa o HTTPS. Depois de desativar o HTTPS, aparecem três etapas de funcionamento no diálogo de domínio personalizado. Quando um passo fica ativo, os detalhes aparecem sob o degrau. Depois da conclusão bem-sucedida de um passo, aparece uma marca de verificação verde junto ao mesmo. 

| Progresso da operação | Detalhes da operação | 
| --- | --- |
| 1 Submeter o pedido | Submeter o pedido |
| 2 Desaprovisionamento do certificado | Eliminar o certificado |
| 3 Concluído | Certificado eliminado |

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

1. *Quem é o fornecedor do certificado e que tipo de certificado é utilizado?*

    Um certificado dedicado fornecido pela Digicert é utilizado para o seu domínio personalizado para:
    
    * **Azure CDN de Verizon**
    * **Azure CDN da Microsoft**

2. *Utiliza TLS/SSL baseado em IP ou em SNI?*

    A **CDN do Azure da Verizon** e a **CDN do Azure Standard da Microsoft** utilizam SNI TLS/SSL.

3. *E se não receber o e-mail de verificação do domínio da DigiCert?*

    Se não estiver a utilizar o subdomínio *cdnverify* e a sua entrada CNAME for para o seu nome de anfitrião de ponto final, não receberá um e-mail de verificação de domínio.
     
    A validação ocorre automaticamente. Caso contrário, se não tiver uma entrada CNAME e não tiver recebido um e-mail passadas 24 horas, contacte o suporte da Microsoft.

4. *A utilização de um certificado SAN é mais insegura do que um certificado dedicado?*
    
    Os certificados SAN seguem as mesmas normas de encriptação e segurança dos certificados dedicados. Todos os certificados TLS/SSL emitidos utilizam SHA-256 para uma segurança reforçada do servidor.

5. *Preciso de um registo Autorização de Autoridade de Certificação junto do meu fornecedor de DNS?*

    O registo de autorização da Autoridade de Certificados não é atualmente necessário. No entanto, se tiver um, o mesmo tem de incluir a DigiCert como AC válida.

6. *Em 20 de junho de 2018, a Azure CDN da Verizon começou a usar um certificado dedicado com SNI TLS/SSL por padrão. O que acontece aos meus domínios personalizados existentes utilizando o certificado Subject Alternative Names (SAN) e o TLS/SSL baseado em IP?*

    Os domínios existentes serão gradualmente migrados para um único certificado nos meses seguintes se a Microsoft analisar que apenas os pedidos de cliente do SNI são realizados na sua aplicação. 
    
    Se os clientes não-SNI forem detetados, os seus domínios permanecem no certificado SAN com TLS/SSL baseado em IP. Os pedidos ao seu serviço ou clientes que não sejam SNI, não são afetados.

7. *Como funcionam as renovações do cert com o Seu Próprio Certificado?*

    Para garantir que um certificado mais recente é implantado na infraestrutura PoP, faça o upload do seu novo certificado para Azure KeyVault. Nas definições de TLS no Azure CDN, escolha a versão mais recente do certificado e selecione guardar. O Azure CDN irá então propagar o seu novo certificado atualizado. 

8. *Tenho de voltar a ativar HTTPS depois do reinício do ponto final?*

    Sim. Se estiver a utilizar **o Azure CDN da Akamai**, se o ponto final parar e reiniciar, tem de voltar a ativar a definição HTTPS se a definição estiver ativa antes.


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
