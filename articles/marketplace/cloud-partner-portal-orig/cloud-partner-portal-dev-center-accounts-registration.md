---
title: Criar uma conta de desenvolvedor da Microsoft | Azure Marketplace
description: Requisitos e etapas para criar uma conta de desenvolvedor da Microsoft.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 522caa53123ad5ccb8076839d2dfa7dc1236b022
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818430"
---
<a name="create-a-microsoft-developer-account"></a>Criar uma conta de desenvolvedor da Microsoft
====================================

Este artigo descreve como se tornar um desenvolvedor da Microsoft aprovado para a publicação do Azure Marketplace.

## <a name="create-a-microsoft-account"></a>Criar uma conta Microsoft

Para iniciar o processo de publicação, você precisará concluir o registro do **centro de desenvolvimento da Microsoft** . Você usará a mesma conta registrada na **[portal do Cloud Partner](https://cloudpartner.azure.com/)** para iniciar o processo de publicação.

### <a name="general-account-guidelines"></a>Diretrizes de conta geral

É recomendável que você tenha apenas um conta Microsoft para suas ofertas do Azure Marketplace. Essa conta não deve ser específica para serviços ou ofertas.

O endereço que forma o nome de usuário deve estar em seu domínio e controlado por sua equipe de ti. Todas as atividades relacionadas à publicação devem ser feitas por meio dessa conta.

>[!WARNING]
>Palavras como "Azure" e "Microsoft" não têm suporte para conta Microsoft registro. Evite usar essas palavras para concluir o processo de criação e registro da conta.

### <a name="company-account-guidelines"></a>Diretrizes de conta da empresa

Siga estas diretrizes se mais de uma pessoa precisar acessar a conta fazendo logon com o conta Microsoft que abriu a conta.

>[!IMPORTANT]
>Para permitir que vários usuários acessem sua conta do centro de desenvolvimento, é recomendável usar Azure Active Directory para atribuir funções a usuários individuais. Eles podem acessar a conta entrando com suas credenciais individuais do Azure AD. Para obter mais informações, consulte [gerenciar usuários da conta](https://docs.microsoft.com/windows/uwp/publish/manage-account-users).

-   Crie seu conta Microsoft usando um endereço de email que pertença ao domínio\'s de sua empresa, mas não a um único indivíduo. Por exemplo, windowsapps\@fabrikam.com.
-   Limite o acesso a esse conta Microsoft ao menor número possível de desenvolvedores.
-   Configure uma lista de distribuição de email corporativo que inclua todos que precisem acessar a conta de desenvolvedor e adicione esse endereço de email às suas informações de segurança. Isso permite que todos os funcionários da lista recebam códigos de segurança quando necessário e gerenciem as informações de segurança de seu conta Microsoft. Se a configuração de uma lista de distribuição não for viável, o proprietário da conta de email individual precisará estar disponível para acessar e compartilhar o código de segurança quando solicitado (por exemplo, quando novas informações de segurança são adicionadas à conta ou quando elas devem ser acessadas de um novo dispositivo).
-   Adicione um número de telefone da empresa que não exija uma extensão e que possa ser acessado pelos principais membros da equipe.
-   Em geral, os desenvolvedores usam dispositivos confiáveis para fazer logon na conta de desenvolvedor da sua empresa. Todos os principais membros da equipe devem ter acesso a esses dispositivos confiáveis. Isso reduzirá a necessidade de que os códigos de segurança sejam enviados ao acessar a conta.
-   Se você precisar permitir o acesso à conta de um PC não confiável, limite o acesso a um máximo de cinco desenvolvedores. O ideal é que esses desenvolvedores acessem a conta de computadores que compartilham o mesmo local geográfico e de rede.
-   Com frequência, revise as [informações de segurança](https://account.live.com/proofs/Manage) da sua empresa para verificar se elas estão atualizadas.

>[!IMPORTANT]
>Sua conta de desenvolvedor deve ser acessada principalmente de PCs confiáveis. Isso é crítico porque há um limite para o número de códigos gerados por conta, por semana. Ele também permite a experiência de entrada mais direta.
>
>Para obter mais informações, consulte [diretrizes e segurança adicionais da conta de desenvolvedor](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="to-create-a-microsoft-account"></a>Para criar um conta Microsoft

1.  Abra um novo Chrome Incognito ou sessão de navegação InPrivate do Internet Explorer para garantir que você não esteja conectado a uma conta existente.
2.  Registre o email (usando as diretrizes anteriores) como um conta Microsoft usando este [link](https://signup.live.com/signup.aspx). Conclua as seguintes instruções de inscrição:

    - Ao registrar sua conta como um conta Microsoft, você precisa fornecer um número de telefone válido para o sistema enviar um código de verificação de conta como uma mensagem de texto ou uma chamada automática.
    - Ao registrar sua conta como um conta Microsoft, você precisa fornecer uma ID de email válida para receber um email automatizado para verificação de conta.
    - Verifique o endereço de email enviado para a DL.

    Agora você está pronto para usar o novo conta Microsoft no centro de desenvolvimento da Microsoft.

## <a name="register-your-account-in-microsoft-developer-center"></a>Registrar sua conta no centro de desenvolvimento da Microsoft

A central de desenvolvedores da Microsoft é usada para registrar as informações da empresa uma vez. O inscrito deve ser um representante válido da empresa e deve fornecer suas informações pessoais como uma maneira de validar sua identidade. A pessoa que está se registrando deve usar um conta Microsoft compartilhado para a empresa **e a mesma conta deve ser usada no portal do Cloud Partner.** Você deve verificar se sua empresa ainda não tem uma conta do centro de desenvolvimento da Microsoft antes de tentar criar uma. Durante o processo, coletaremos informações de endereço da empresa, informações de conta bancária e informações fiscais. Normalmente, estas são obtidas por meio de contactos financeiros ou profissionais.

>[!IMPORTANT]
>Você deve concluir os seguintes componentes de perfil de desenvolvedor para progredir pelas várias fases de criação e implantação de oferta.

| Perfil do desenvolvedor     | Para iniciar o rascunho    | Teste       | Publicar modelo gratuito e de solução   | Publicar comercialmente   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Registro da empresa  | Deve ter         | Deve ter     | Deve ter                             | Deve ter             |
| ID do perfil de imposto        | Opcional          | Opcional      | Opcional                              | Deve ter             |
| Conta bancária          | Opcional          | Opcional      | Opcional                              | Deve ter             |

>[!NOTE]
>Há suporte para BYOL (traga sua própria licença) apenas para máquinas virtuais e é considerado uma oferta gratuita.

### <a name="register-your-company-account"></a>Registrar sua conta da empresa

1. Abra uma nova sessão de navegação do Internet Explorer InPrivate ou Chrome Incognito para garantir que você não esteja conectado a uma conta pessoal.

2. Vá para o [centro de desenvolvimento do Windows](https://dev.windows.com/registration?accountprogram=azure) para se registrar como um vendedor. Leia a observação importante a seguir antes de continuar.

   ![Verificação de conta Microsoft](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Verifique se a ID de email ou a lista de distribuição (uma lista de distribuição é recomendada para remover a dependência de indivíduos) que você usará para registrar no centro de desenvolvimento é registrada pela primeira vez como um conta Microsoft. Caso contrário, registre-se usando este link. Além disso, qualquer ID de email no domínio da empresa da Microsoft não pode ser usada para o registro do centro de desenvolvimento. '

   ![Entrada do centro de desenvolvimento](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Execute o assistente "ajude-nos a proteger sua conta" para verificar sua identidade usando um número de telefone ou endereço de email.

4. Em registro-informações da conta, selecione o **país/região** da sua conta na lista suspensa e, em seguida, selecione **Avançar**.

   ![Selecionar país/região](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >"Vender-de" países/regiões: para vender seus serviços no Azure Marketplace, sua entidade registrada precisa ser de um dos países/regiões "vender-de" aprovados mostrados na lista suspensa. Essa restrição destina-se a motivos de pagamento e tributação. Para obter mais informações, consulte as políticas de participação no Marketplace.

5. Selecione **empresa** como seu "tipo de conta" e, em seguida, selecione **Avançar**.

    >[!IMPORTANT]
    >Para entender melhor os tipos de conta e decidir qual tipo é melhor para você, exiba tipos de conta de página, locais e taxas mostrados na próxima captura de tela.

    ![Tipos de conta para vendedores](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Insira o **nome de exibição do editor**. Normalmente, esse é o nome da sua empresa.

    >[!NOTE]
    >O nome de exibição do Publicador inserido no centro de desenvolvimento não será exibido no Azure Marketplace depois que sua oferta for listada. Mas essas informações são necessárias para concluir o processo de registro.

7. Insira as **informações de contato** para a verificação da conta.

    >[!IMPORTANT]
    >Você deve fornecer informações de contato precisas, pois elas serão usadas em nosso processo de verificação para que sua empresa seja aprovada no centro de desenvolvedores.

8. Insira as informações de contato para o **Aprovador da empresa**. O aprovador da empresa é a pessoa que pode verificar se você está autorizado a criar uma conta no centro de desenvolvimento em nome da sua organização. Depois de fornecer essas informações, selecione **Avançar** para ir para a **seção pagamento**.

    ![Identificar aprovador da empresa](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Insira as informações de pagamento para sua conta. Se você tiver um código promocional que cubra o custo do registro, você pode inseri-lo aqui. Caso contrário, forneça suas informações de cartão de crédito (ou o PayPal em mercados com suporte). Selecione **Avançar** para passar para a **revisão**final.

   ![Registro de pagamento](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Examine as informações da conta e confirme se tudo está correto. Leia e aceite os termos e condições do [Microsoft Azure Marketplace contrato de editor](https://go.microsoft.com/fwlink/?LinkID=699560). Marque a caixa para indicar que você leu e aceitou estes termos.

11. Selecione **concluir** para confirmar seu registro. Uma mensagem de confirmação é enviada para seu endereço de email.

12. Se você estiver planejando apenas publicar ofertas gratuitas, selecione [ir para a Portal do Cloud Partner](https://cloudpartner.azure.com/) e vá para "registrar sua conta no portal de parceiros de nuvem" neste artigo.

### <a name="commercial-offers"></a>Ofertas comerciais

Se estiver planejando publicar ofertas comerciais, como uma oferta de máquina virtual usando um modelo de cobrança por hora, você precisará fornecer informações de impostos e bancários. Para isso, entre na sua conta do centro de desenvolvedores e selecione **atualizar suas informações de conta**. Siga as instruções na próxima seção, "adicionar informações bancárias e fiscais".

>[!IMPORTANT]
>Você não poderá enviar por push uma oferta comercial para produção sem fornecer informações de contas bancárias e impostos.

Se você preferir atualizar suas informações bancárias e de impostos mais tarde, poderá pular para "registrar sua conta no portal de parceiros de nuvem" neste artigo.

>[!NOTE]
>É recomendável fornecer informações de conta bancária e impostos o mais rápido possível, pois leva tempo para validar informações fiscais.

### <a name="add-banking-and-tax-information"></a>Adicionar informações bancárias e fiscais

Para publicar ofertas comerciais para compra, você precisa adicionar informações de pagamento e impostos e enviá-las para validação no centro de desenvolvedores.

**Para fornecer informações bancárias**

1.  Entre no centro de [desenvolvedores da Microsoft](https://dev.windows.com/registration?accountprogram=azure) com seu conta Microsoft.
2.  Selecione **conta** de pagamento no menu à esquerda, em **escolher método de pagamento**, selecione **conta bancária** ou **paypal**.

    >[!NOTE]
    >Se você tiver ofertas comerciais que os clientes compram no Marketplace, essa será a conta em que você receberá pagamento para essas compras.
3.  Insira as informações de pagamento e, em seguida, selecione **salvar**.

    >[!IMPORTANT]
    >Se você precisar atualizar ou alterar sua conta de pagamento, siga as etapas anteriores para substituir as informações atuais pelas novas informações.
    >
    >Alterar sua conta de pagamento pode atrasar seus pagamentos em até um ciclo de pagamento. Esse atraso ocorre porque precisamos verificar a alteração da conta, exatamente como fizemos quando você configurou a conta de pagamento pela primeira vez. Você ainda será pago pelo valor total depois que sua conta tiver sido verificada; todos os pagamentos devidos ao ciclo de pagamento atual serão adicionados ao próximo.

4.  Selecione **Seguinte**.

**Para fornecer informações de impostos**

1.  Entre no centro de [desenvolvimento da Microsoft](https://dev.windows.com/registration?accountprogram=azure) com seu conta Microsoft (se necessário).
2.  No menu à esquerda, selecione **perfil de imposto**.
3.  Na página **configurar seu formulário de imposto** :
    - Selecione o país ou região em que você tem residência permanente.
    - Selecione o país ou região onde você mantém a cidadania primária.
    - Selecione **Seguinte**.
4.  Insira os detalhes do imposto e, em seguida, selecione **Avançar**.

>[!WARNING]
>Você não poderá enviar por push para suas ofertas comerciais para produção sem fornecer informações de contas bancárias e impostos em sua conta do centro de desenvolvedores da Microsoft.

### <a name="developer-center-registration-issues"></a>Problemas de registro do centro de desenvolvedores

Se você tiver problemas com o registro do centro de desenvolvedores, use as etapas a seguir para abrir um tíquete de suporte.

1.  Vá para o [link de suporte](https://developer.microsoft.com/windows/support).
2.  Em **Fale conosco**, selecione **enviar um incidente**.
    ![abrir um tíquete](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  Para **tipo de problema**, selecione "ajuda com o centro de desenvolvimento" e, para **categoria**, selecione "publicar e gerenciar aplicativos". Selecione **Iniciar email**.

    ![identificar o tipo de problema](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Você receberá uma página de entrada. Use qualquer conta Microsoft para entrar. Se você não tiver um conta Microsoft, [crie um](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1). \

5.  Forneça informações detalhadas sobre o problema e selecione **Enviar** para enviar o tíquete.

    ![enviar um tíquete](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>Registrar sua conta no portal de parceiros de nuvem

Você usa o [portal do Cloud Partner](https://cloudpartner.azure.com/) para publicar e gerenciar suas ofertas.

1.  Abra um novo Chrome Incognito ou sessão de navegação InPrivate do Internet Explorer para garantir que você não esteja conectado a uma conta pessoal.
2.  Vá para [portal do Cloud Partner](https://cloudpartner.azure.com/).
3.  Se você for um novo usuário e estiver entrando no [portal do Cloud Partner](https://cloudpartner.azure.com/) pela primeira vez, deverá entrar usando a mesma ID de email registrada com sua conta do centro de desenvolvimento. Isso garante que sua conta do centro de desenvolvimento e a conta do portal de parceiros de nuvem estejam vinculadas entre si.

Posteriormente, você pode adicionar os outros membros da empresa que estão trabalhando no aplicativo. Você pode usá-los como colaboradores ou proprietários no portal de parceiros de nuvem seguindo as etapas na próxima seção.

Se você for adicionado como colaborador/proprietário no portal do portal de parceiros de nuvem, você poderá entrar com sua própria conta.

>[!TIP]
>As políticas de participação são descritas no site do Azure.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Gerenciar usuários como proprietários ou colaboradores no portal de parceiros de nuvem

[Etapas para gerenciar usuários no portal de parceiros de nuvem](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>Passos seguintes

Agora que sua conta foi criada e registrada, você pode iniciar o processo de publicação do Azure Marketplace.
