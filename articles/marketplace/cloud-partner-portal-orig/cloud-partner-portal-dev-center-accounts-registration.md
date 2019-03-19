---
title: Criar uma conta do Microsoft Developer | Documentos da Microsoft
description: Requisitos e passos para criar uma conta do Microsoft Developer.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: adfe0af646fae79bc9a954cd5d654626880915d3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894092"
---
<a name="create-a-microsoft-developer-account"></a>Criar uma conta do Microsoft Developer
====================================

Este artigo descreve como se tornar um Microsoft Developer aprovados para publicação do Azure Marketplace.

## <a name="create-a-microsoft-account"></a>Criar uma conta Microsoft

Para iniciar o processo de publicação, terá de concluir o **Microsoft Developer Center** registo. Vai utilizar a mesma conta registada no **[Cloud Partner Portal](https://cloudpartner.azure.com/)** para iniciar o processo de publicação.

### <a name="general-account-guidelines"></a>Diretrizes gerais de conta

Recomendamos que ter apenas uma conta Microsoft para as ofertas do Azure Marketplace. Esta conta não deve ser específica para serviços ou ofertas.

O endereço que forma o nome de utilizador deve fazer no seu domínio e ser controlado pela sua equipa de TI. Todas as atividades relacionadas com publicação devem ser feitas através desta conta.

>[!WARNING]
>Palavras como "Azure" e "Microsoft" não são suportadas para o registo de conta Microsoft. Evite utilizar essas palavras para concluir a criação da conta e o processo de registo.

### <a name="company-account-guidelines"></a>Diretrizes de conta de empresa

Siga essas diretrizes se mais de uma pessoa terá de aceder à conta ao iniciar sessão com a conta da Microsoft que abriu a conta.

>[!IMPORTANT]
>Para permitir que vários utilizadores aceder à sua conta do Dev Center, recomendamos que utilize o Azure Active Directory para atribuir funções a utilizadores individuais. Podem aceder a conta ao iniciar sessão com o indivíduo credenciais do Azure AD. Para obter mais informações, consulte [gerir os utilizadores da conta](https://docs.microsoft.com/windows/uwp/publish/manage-account-users).

-   Criar a sua conta Microsoft através de um endereço de e-mail que pertence a sua empresa\'domínio s, mas não para uma única pessoa. Por exemplo, windowsapps\@fabrikam.com.
-   Limitar o acesso a esta conta Microsoft para o menor número possível de desenvolvedores.
-   Configurar uma lista de distribuição de e-mail da empresa que inclui todas as pessoas que precisa para aceder à conta de desenvolvedor e adicionar este endereço de e-mail às suas informações de segurança. Isso permite que todos os funcionários da lista para receber códigos de segurança, quando necessário e gerir informações de segurança da sua conta da Microsoft. Se configurar uma lista de distribuição não é viável, o proprietário da conta de individual email tem de estar disponível para acessar e compartilhar o código de segurança quando lhe for pedido (por exemplo, quando novas informações de segurança é adicionada à conta ou quando deve ser acessado de um dispositivo novo.)
-   Adicione um número de telefone da empresa que não requer uma extensão e está acessível para os membros da equipe de chave.
-   Em geral, têm os desenvolvedores usem dispositivos fidedignos para iniciar sessão na conta de programador da sua empresa. Todos os membros de chave da Equipe devem ter acesso a estes dispositivos fidedignos. Isto irá reduzir a necessidade de códigos de segurança a serem enviados ao acessar a conta.
-   Se tiver de permitir o acesso à conta de um PC não fidedigno, limite o que o acesso a um máximo de cinco desenvolvedores. O ideal é que esses desenvolvedores devem aceder à conta a partir de máquinas que partilham o mesmo geográfica e a localização de rede.
-   Com frequência rever seus [informações de segurança da empresa](https://account.live.com/proofs/Manage) para se certificar de que é atual.

>[!IMPORTANT]
>Sua conta de programador deve ser acessada principalmente a partir de PCs fidedignos. Isto é fundamental porque existe um limite ao número de códigos gerados por conta e, por semana. Também permite que a experiência de início de sessão totalmente integrada.
>
>Para obter mais informações, consulte [diretrizes de conta de desenvolvedor adicionais e segurança](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="to-create-a-microsoft-account"></a>Para criar uma conta Microsoft

1.  Abra um novo Chrome Incognito ou Internet Explorer InPrivate sessão de navegação para se certificar de que não estiver inscrito uma conta existente.
2.  Registe-se a mensagem de e-mail (com as diretrizes anteriores) como uma conta Microsoft ao utilizar isto [link](https://signup.live.com/signup.aspx). Conclua o início de sessão seguintes instruções de instalação:

    - Ao registar a sua conta como uma conta Microsoft, tem de indicar um número de telefone válido para o sistema para lhe enviar um código de verificação de conta como uma mensagem de texto ou chamada automática.
    - Ao registar a sua conta como uma conta Microsoft, tem de fornecer um id de e-mail válido para o recebimento de uma mensagem de e-mail automatizada para verificação da conta.
    - Verifique se o endereço de e-mail enviado à DL.

    Agora, está pronto para utilizar a nova conta Microsoft no Microsoft Developer Center.

## <a name="register-your-account-in-microsoft-developer-center"></a>Registar a sua conta no Microsoft Developer Center

O Microsoft Developer Center é utilizado para registar as informações da empresa uma vez. O registo tem de ser um representante válido da empresa e tem de fornecer suas informações pessoais como uma forma de validar a sua identidade. A pessoa a registar tem de utilizar uma conta Microsoft que é partilhada para a empresa, **e a mesma conta deve ser utilizada no Portal de parceiros de nuvem.** Deve verificar para se certificar de que a sua empresa ainda não tiver uma conta do Microsoft Developer Center antes de tentar criar uma. Durante o processo, iremos recolher informações de endereço da empresa, informações de conta bancária e informações de imposto sobre vendas. Normalmente, estas são obtidas por meio de contactos financeiros ou profissionais.

>[!IMPORTANT]
>Tem de concluir os seguintes componentes de perfil do desenvolvedor para percorre as fases de criação de oferta e a implantação.

| Perfil do desenvolvedor     | Para iniciar o draft    | Teste       | Publicar gratuitamente e o modelo de solução   | Publicar comercial   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Registo de empresa  | Tem de ter         | Tem de ter     | Tem de ter                             | Tem de ter             |
| ID de perfil do imposto        | Opcional          | Opcional      | Opcional                              | Tem de ter             |
| Conta bancária          | Opcional          | Opcional      | Opcional                              | Tem de ter             |

>[!NOTE]
>Traga a sua própria licença (BYOL) só é suportado para máquinas virtuais e é considerada uma oferta gratuita.

### <a name="register-your-company-account"></a>Registe-se a sua conta da empresa

1. Abra um novo Internet Explorer InPrivate ou Incognito Chrome sessão de navegação para se certificar de que não estiver inscrito para uma conta pessoal.

2. Vá para o [Windows Dev Center](https://dev.windows.com/registration?accountprogram=azure) para registrar-se como um vendedor. Leia a observação importante a seguir antes de continuar.

   ![Verificação da conta Microsoft](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Certifique-se de que o e-mail id ou lista de distribuição (uma lista de distribuição é recomendada para remover a dependência de indivíduos) que irá utilizar para se registrar no Centro de desenvolvimento está no primeiro registado como uma conta Microsoft. Caso contrário, em seguida, registe-se utilizando a seguinte hiperligação. Além disso, qualquer id de e-mail, sob o domínio de empresa da Microsoft não pode ser utilizado para o registo do Centro de desenvolvimento. "

   ![Início de sessão de centro de desenvolvimento](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Execute o assistente "Ajude-na proteger a sua conta", para verificar a sua identidade através de um endereço de e-mail ou número de telefone.

4. Em informações de conta de registo, selecione seu **país/região da conta** na lista pendente e, em seguida, selecione **próxima**.

   ![Selecione o país/região](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >Países de "Destino de venda-de": Para vender os seus serviços no Azure Marketplace, a sua entidade registada tem de ser de um dos países aprovados "destino de venda-de" mostrados na lista pendente. Esta restrição é por motivos de dividendos e tributação. Para obter mais informações, consulte as políticas de participação do Marketplace.

5. Selecione **empresa** como o "Tipo de conta" e selecione **próxima**.

    >[!IMPORTANT]
    >Para melhor compreender os tipos de conta e decidir que tipo é melhor para, ver os tipos de conta de página, locais e taxas de mostrado na captura de ecrã seguinte.

    ![Tipos de conta para os vendedores](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Introduza o **nome a apresentar do publicador**. Isto é, normalmente, o nome da sua empresa.

    >[!NOTE]
    >O nome a apresentar publicador introduzido no Centro de desenvolvimento não é apresentado no Azure Marketplace depois da oferta está listada. Mas estas informações são necessárias para concluir o processo de registo.

7. Introduza o **informações de contacto** para a verificação da conta.

    >[!IMPORTANT]
    >Tem de fornecer informações de contacto exatas como vai ser utilizada em nosso processo de verificação para a sua empresa para ser aprovado no Centro de programadores. "

8. Introduza as informações de contacto para o **aprovador da empresa**. O aprovador da empresa é a pessoa que pode verificar que está autorizado a criar uma conta no Centro de desenvolvimento em nome da sua organização. Depois de fornecer estas informações, selecione **próxima** mover para o **seção de pagamento**.

    ![Identificar o aprovador da empresa](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Introduza as informações de pagamento para a sua conta. Se tiver um código promocional, que abrange o custo do Registro, pode inserir que aqui. Caso contrário, fornece suas informações de cartão de crédito (ou o PayPal em mercados suportados). Selecione **próxima** para avançar para o último **revisão**.

   ![Registo de pagamento](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Reveja as suas informações de conta e confirme que tudo está correta. Leia e aceite os termos e condições do [contrato de publicador do Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560). Selecione a caixa para indicar que li e Aceito estes termos.

11. Selecione **concluir** para confirmar o seu registo. Uma mensagem de confirmação é enviada para o seu endereço de e-mail.

12. Se estiver a planear apenas publicar ofertas gratuitas, selecione [vá para o Portal de parceiro de Cloud](https://cloudpartner.azure.com/) e avance para o "Registar a sua conta no portal de parceiros de cloud" neste artigo.

### <a name="commercial-offers"></a>Ofertas comerciais

Se estiver a planear publicar ofertas comerciais, como uma oferta de Máquina Virtual através de uma hora a hora de faturação modelo, terá de fornecer fiscais e bancárias informações. Para isso, inicie sessão na sua conta do Centro de programadores e selecione **atualizar as suas informações de conta**. Siga as instruções na secção seguinte, "Adicionar serviços bancários e imposto sobre informações".

>[!IMPORTANT]
>Não será capaz de emitir uma oferta comercial para produção sem fornecer informações fiscais e de conta bancária.

Se atualizar seu banco e imposto sobre vendas informações mais tarde, em seguida, pode avançar para "Registar a sua conta no portal de parceiros de cloud" neste artigo.

>[!NOTE]
>É recomendável fornecer conta bancária e informações de imposto sobre logo que possível, porque demora algum tempo para validar informações fiscais.

### <a name="add-banking-and-tax-information"></a>Adicionar a banca e informações de imposto sobre vendas

Para publicar ofertas comerciais para compra, terá de adicionar fiscais e informações de imposto sobre vendas e enviá-lo para a validação no Centro de programadores.

**Para fornecer informações bancárias**

1.  Inicie sessão para o [Microsoft Developer Center](https://dev.windows.com/registration?accountprogram=azure) com a sua conta Microsoft.
2.  Selecione **conta de dividendos** no menu à esquerda, sob **escolher método de pagamento**, selecione **conta bancária** ou **PayPal**.

    >[!NOTE]
    >Se tiver ofertas comerciais que os clientes de compra no Marketplace, esta é a conta em que receberá dividendos para essas compras.
3.  Introduza as informações de pagamento e, em seguida, selecione **guardar**.

    >[!IMPORTANT]
    >Se precisar de atualizar ou alterar a sua conta de dividendos, siga os passos anteriores para substituir as informações atuais com novas informações.
    >
    >Alterar a sua conta de dividendos pode atrasar os pagamentos pelo máximo de um ciclo de pagamento. Este atraso ocorre porque precisamos de verificar a alteração da conta, tal como foi feito ao configurar a conta de dividendos pela primeira vez. Ainda vai ser pago pela quantia total após a verificação da sua conta; qualquer pagamentos devido para pagamento atual ciclo será adicionado para o seguinte.

4.  Selecione **Seguinte**.

**Para fornecer informações fiscais**

1.  Inicie sessão para o [Microsoft Developer Center](https://dev.windows.com/registration?accountprogram=azure) com a sua conta Microsoft (se necessário).
2.  No menu da esquerda, selecione **imposto sobre vendas perfil**.
3.  Sobre o **configurar o seu formulário de imposto sobre** página:
    - Selecione o país ou região onde tenha residência permanente.
    - Selecione o país ou região onde mantém cidadania primária.
    - Selecione **Seguinte**.
4.  Introduza os detalhes de imposto e, em seguida, selecione **seguinte**.

>[!WARNING]
>Não será possível enviar suas ofertas comercial para produção sem fornecer informações fiscais na sua conta do Microsoft Developer Center e de conta bancária.

### <a name="developer-center-registration-issues"></a>Problemas de registo do Centro de programadores

Se tiver problemas com o registo de centro de programadores, utilize os seguintes passos para abrir um pedido de suporte.

1.  Vá para o [ligação de suporte](https://developer.microsoft.com/windows/support).
2.  Sob **contacte-nos**, selecione **submeter um incidente**.
    ![Abra um pedido de suporte](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  Para **tipo de problema**, selecione "Ajudar com o Centro de desenvolvimento" e para **categoria**, selecione "publicar e gerir aplicações". Selecione **iniciar e-mail**.

    ![identificar o tipo de problema](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Receberá um início de sessão na página. Utilize qualquer conta Microsoft para iniciar sessão. Se não tiver uma conta Microsoft, em seguida, [criá-lo](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1). \

5.  Fornecer informações detalhadas sobre o problema e selecione **submeter** para enviar o pedido de suporte.

    ![enviar um pedido de suporte](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>Registar a sua conta no portal de parceiros de nuvem

Utilizar o [Cloud Partner Portal](https://cloudpartner.azure.com/) para publicar e gerir a sua offer(s).

1.  Abra um novo Chrome Incognito ou Internet Explorer InPrivate sessão de navegação para se certificar de que não estiver inscrito para uma conta pessoal.
2.  Aceda a [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).
3.  Se for um novo utilizador e iniciar sessão para o [Cloud Partner Portal](https://cloudpartner.azure.com/) pela primeira vez, em seguida, tem de iniciar sessão com o mesmo id de e-mail está registado com a sua conta do Dev Center. Isto garante que a sua conta do Dev Center e a conta de portal de parceiro de cloud estão ligados entre si.

Mais tarde pode adicionar os outros membros da empresa, que estão a trabalhar no aplicativo. Pode adicioná-las como contribuintes ou proprietários no portal de parceiros de nuvem ao seguir os passos na secção seguinte.

Se forem adicionados como um proprietário/Contribuidor no portal do portal de parceiros da cloud, em seguida, pode iniciar sessão com sua própria conta.

>[!TIP]
>As políticas de participação são descritas no site do Azure.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Gerir utilizadores como proprietários ou contribuidores no portal de parceiros de nuvem

[Passos para gerir utilizadores no portal de parceiros da cloud](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>Passos Seguintes

Agora que sua conta é criada e registrada, pode iniciar o Azure marketplace, processo de publicação.
