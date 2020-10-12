---
title: Resolução de problemas redefinição da palavra-passe de autosserviço - Azure Ative Directory
description: Saiba como resolver problemas comuns e etapas de resolução para a redefinição da palavra-passe de autosserviço no Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a02d46688fa49401684f836a7c289906affb413
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90030026"
---
# <a name="troubleshoot-self-service-password-reset-in-azure-active-directory"></a>Resolução de problemas de autosserviço redefinição de senha no Azure Ative Directory

O Azure Ative Directory (Azure AD) redefiniu a palavra-passe de autosserviço (SSPR) permite que os utilizadores republem as suas palavras-passe na nuvem.

Se tiver problemas com a SSPR, as seguintes etapas de resolução de problemas e erros comuns podem ajudar. Se não conseguir encontrar a resposta para o seu problema, [as nossas equipas de apoio estão sempre disponíveis](#contact-microsoft-support) para o ajudar mais.

## <a name="sspr-configuration-in-the-azure-portal"></a>Configuração SSPR no portal Azure

Se tiver problemas em ver ou configurar opções SSPR no portal Azure, reveja as seguintes etapas de resolução de problemas:

### <a name="i-dont-see-the-password-reset-section-under-azure-ad-in-the-azure-portal"></a>Não vejo a secção **de reset da palavra-passe** sob a Azure AD no portal Azure.

Não verá se **a palavra-passe reinicia** a opção do menu se não tiver uma licença AD Azure atribuída ao administrador que executa a operação.

Para atribuir uma licença à conta do administrador em questão, siga os passos para [atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="i-dont-see-a-particular-configuration-option"></a>Não vejo uma opção de configuração específica.

Muitos elementos da UI estão escondidos até serem necessários. Certifique-se de que a opção está ativada antes de procurar as opções de configuração específicas.

### <a name="i-dont-see-the-on-premises-integration-tab"></a>Não vejo o separador **de integração no local.**

A gravação de passwords no local só é visível se tiver descarregado o Azure AD Connect e tiver configurado a funcionalidade.

Para obter mais informações, consulte [Começar com a Azure AD Connect](../hybrid/how-to-connect-install-express.md).

## <a name="sspr-reporting"></a>Reportagem SSPR

Se tiver problemas com relatórios SSPR no portal Azure, reveja as seguintes etapas de resolução de problemas:

### <a name="i-dont-see-any-password-management-activity-types-in-the-self-service-password-management-audit-event-category"></a>Não vejo nenhum tipo de atividade de gestão de passwords na categoria de eventos de auditoria **de Autosserviço password Management.**

Isto pode acontecer se não tiver uma licença AZure AD atribuída ao administrador que executa a operação.

Para atribuir uma licença à conta do administrador em questão, siga os passos para [atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="user-registrations-show-multiple-times"></a>As inscrições dos utilizadores mostram várias vezes.

Quando um utilizador se regista, registamos atualmente cada pedaço de dados individual que está registado como um evento separado.

Se quiser agregar estes dados e ter uma maior flexibilidade na forma como os pode visualizar, pode descarregar o relatório e abrir os dados como uma mesa de rotação no Excel.

## <a name="sspr-registration-portal"></a>Portal de registo SSPR

Se os seus utilizadores tiverem problemas de registo para sSPR, reveja as seguintes etapas de resolução de problemas:

### <a name="the-directory-isnt-enabled-for-password-reset-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>O diretório não está habilitado para reset de senha. O utilizador pode ver um erro que relata: "O seu administrador não lhe permitiu utilizar esta funcionalidade."

Pode ativar sSPR para todos os utilizadores, sem utilizadores ou para grupos selecionados de utilizadores. Apenas um grupo AZure AD pode atualmente ser ativado para SSPR utilizando o portal Azure. Como parte de uma implantação mais ampla da SSPR, os grupos aninhados são apoiados. Certifique-se de que os utilizadores do grupo que escolher têm as licenças apropriadas atribuídas.

No portal Azure, altere a configuração **de autosserviço de redefinição ativada** para *Selecionado* ou *Tudo* e, em seguida, selecione **Guardar**.

### <a name="the-user-doesnt-have-an-azure-ad-license-assigned-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>O utilizador não tem uma licença AD Azure atribuída. O utilizador pode ver um erro que relata: "O seu administrador não lhe permitiu utilizar esta funcionalidade."

Apenas um grupo AZure AD pode atualmente ser ativado para SSPR utilizando o portal Azure. Como parte de uma implantação mais ampla da SSPR, os grupos aninhados são apoiados. Certifique-se de que os utilizadores do grupo que escolher têm as licenças apropriadas atribuídas. Reveja o passo anterior de resolução de problemas para permitir a SSPR, conforme necessário.

Reveja também as etapas de resolução de problemas para garantir que o administrador que executa as opções de configuração tem uma licença atribuída. Para atribuir uma licença à conta do administrador em questão, siga os passos para [atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="theres-an-error-processing-the-request"></a>Há um erro no processamento do pedido.

Os erros genéricos de registo SSPR podem ser causados por muitos problemas, mas geralmente este erro é causado por uma falha de serviço ou por um problema de configuração. Se continuar a ver este erro genérico quando voltar a tentar o processo de registo SSPR, contacte o [suporte da Microsoft](#contact-microsoft-support) para obter assistência adicional.

## <a name="sspr-usage"></a>Utilização SSPR

Se você ou os seus utilizadores tiverem problemas em utilizar o SSPR, reveja os seguintes cenários de resolução de problemas e etapas de resolução:

| Erro | Solução |
| --- | --- |
| O diretório não está habilitado para reset de senha. | No portal Azure, altere a configuração **de autosserviço de redefinição ativada** para *Selecionado* ou *Tudo* e, em seguida, selecione **Guardar**. |
| O utilizador não tem uma licença AD Azure atribuída. | Isto pode acontecer se não tiver uma licença AZure AD atribuída ao utilizador pretendido. Para atribuir uma licença à conta do administrador em questão, siga os passos para [atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses). |
| O diretório está ativado para reset de palavra-passe, mas o utilizador tem informações de autenticação em falta ou mal formadas. | Certifique-se de que o utilizador formou corretamente dados de contacto no ficheiro no diretório. Para obter mais informações, consulte [os dados utilizados pela Azure AD, redefinindo a palavra-passe de autosserviço](howto-sspr-authenticationdata.md). |
| O diretório está ativado para reset de palavra-passe, mas o utilizador tem apenas um pedaço de dados de contacto no ficheiro quando a política está definida para exigir dois métodos de verificação. | Certifique-se de que o utilizador dispõe de pelo menos dois métodos de contacto devidamente configurados. Um exemplo é ter um número de telemóvel *e* um número de telefone do escritório. |
| O diretório está ativado para o reset da palavra-passe e o utilizador está devidamente configurado, mas o utilizador não pode ser contactado. | Isto pode ser o resultado de um erro de serviço temporário ou se houver dados de contacto incorretos que não possamos detetar corretamente. <br> <br> Se o utilizador esperar 10 segundos, é apresentado um link para "Tentar novamente" e "Contacte o seu administrador". Se o utilizador selecionar "Tente novamente", retriciona a chamada. Se o utilizador selecionar "Contacte o seu administrador", envia um e-mail de formulário aos administradores solicitando um reset de palavra-passe para ser executado para essa conta de utilizador. |
| O utilizador nunca recebe a palavra-passe de redefinição de SMS ou chamada telefónica. | Isto pode ser o resultado de um número de telefone mal formado no diretório. Certifique-se de que o número de telefone está no formato "+1 4251234567". <br> <br>O reset da palavra-passe não suporta extensões, mesmo que especifique uma no diretório. As extensões são retiradas antes da chamada ser feita. Utilize um número sem extensão ou integre a extensão no número de telefone na sua bolsa de filial privada (PBX). |
| O utilizador nunca recebe o e-mail de reset da palavra-passe. | A causa mais comum para este problema é que a mensagem é rejeitada por um filtro de spam. Verifique a pasta de spam, lixo ou itens eliminados para obter o e-mail. <br> <br> Além disso, certifique-se de que o utilizador verifica a conta de e-mail correta como registada na SSPR. |
| Estabeleci uma política de reset de passwords, mas quando uma conta de administração usa a palavra-passe reposta, essa apólice não é aplicada. | A Microsoft gere e controla a política de reset da palavra-passe do administrador para garantir o mais alto nível de segurança. |
| O utilizador está impedido de tentar uma palavra-passe reiniciada demasiadas vezes num dia. | Um mecanismo de estrangulamento automático é utilizado para impedir que os utilizadores tentem redefinir as suas palavras-passe demasiadas vezes num curto espaço de tempo. O estrangulamento ocorre nos seguintes cenários: <br><ul><li>O utilizador tenta validar um número de telefone cinco vezes numa hora.</li><li>O utilizador tenta utilizar o portão de perguntas de segurança cinco vezes numa hora.</li><li>O utilizador tenta redefinir uma palavra-passe para a mesma conta de utilizador cinco vezes numa hora.</li></ul>Se um utilizador encontrar este problema, deve esperar 24 horas após a última tentativa. O utilizador pode então redefinir a sua palavra-passe. |
| O utilizador vê um erro ao validar o seu número de telefone. | Este erro ocorre quando o número de telefone introduzido não corresponde ao número de telefone no ficheiro. Certifique-se de que o utilizador está a introduzir o número de telefone completo, incluindo a área e o código do país, quando tentar utilizar um método baseado no telefone para reiniciar a palavra-passe. |
| Há um erro no processamento do pedido. | Os erros genéricos de registo SSPR podem ser causados por muitos problemas, mas geralmente este erro é causado por uma falha de serviço ou por um problema de configuração. Se continuar a ver este erro genérico ao tentar novamente o processo de registo SSPR, contacte o [suporte da Microsoft](#contact-microsoft-support) para obter assistência adicional. |
| Violação da política no local | A palavra-passe não corresponde à política de senha do Ative Directory no local. O utilizador deve definir uma palavra-passe que satisfaça os requisitos de complexidade ou força. |
| A palavra-passe não cumpre a política confusa | A palavra-passe que foi usada aparece na [lista de senhas proibidas](./concept-password-ban-bad.md#how-are-passwords-evaluated) e não pode ser usada. O utilizador deve definir uma palavra-passe que cumpra ou exceda a política da lista de palavras-passe proibidas. |

## <a name="sspr-errors-that-a-user-might-see"></a>Erros SSPR que um utilizador pode ver

Os seguintes erros e detalhes técnicos podem ser mostrados a um utilizador como parte do processo SSPR. Muitas vezes, o erro não é algo que possam resolver por si mesmos, uma vez que a funcionalidade SSPR precisa de ativar, configurar ou registar para a sua conta.

Utilize as seguintes informações para compreender o problema e o que precisa de ser corrigido no inquilino AZURE ou na conta de utilizador individual.

| Erro | Detalhes | Detalhes técnicos |
| --- | --- | --- |
| InquilinoSSSPRFlagDisabled = 9 | Lamentamos que não possa redefinir a sua palavra-passe neste momento porque o seu administrador desativou a redefinição de password para a sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Entre em contato com o seu administrador e peça-lhe para ativar esta funcionalidade.<br /><br />Para saber mais, consulte [a Ajuda, esqueci-me da minha senha AD Azure.](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions) | SSPR_0009: Detetámos que o reset da palavra-passe não foi ativado pelo seu administrador. Entre em contato com o seu administrador e peça-lhes para ativar a redefinição de senha para a sua organização. |
| WritebackNotEnabled = 10 |Lamentamos que não possa redefinir a sua palavra-passe neste momento porque o seu administrador não permitiu um serviço necessário para a sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Por favor contacte o seu administrador e peça-lhes para verificarem a configuração da sua organização.<br /><br />Para saber mais sobre este serviço necessário, consulte configurar a gravação [da palavra-passe](./tutorial-enable-sspr-writeback.md). | SSPR_0010: Detetámos que a gravação da palavra-passe não foi ativada. Por favor contacte o seu administrador e peça-lhes para ativar a gravação de senha. |
| SsprNotEnabledInUserPolicy = 11 | Lamentamos que não possa redefinir a sua palavra-passe neste momento porque o seu administrador não configura a redefinição de senha para a sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Contacte o seu administrador e peça-lhes para configurar a redefinição da palavra-passe.<br /><br />Para saber mais sobre a configuração de reset da palavra-passe, consulte [Quickstart: Azure AD self-service reset](./tutorial-enable-sspr.md). | SSPR_0011: A sua organização não definiu uma política de reset de palavra-passe. Por favor contacte o seu administrador e peça-lhes que definam uma política de reset de palavra-passe. |
| UserNotLicensed = 12 | Lamentamos, não pode redefinir a sua senha neste momento porque faltam licenças necessárias à sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Por favor contacte o seu administrador e peça-lhes para verificarem a sua atribuição de licença.<br /><br />Para saber mais sobre o licenciamento, consulte [os requisitos de licenciamento para a Azure AD autosserviço de autosserviço reset](./concept-sspr-licensing.md). | SSPR_0012: A sua organização não tem as licenças necessárias para efetuar a redefinição da palavra-passe. Por favor contacte o seu administrador e peça-lhes que revejam as atribuições de licença. |
| UserNotMemberOfScopedAccessGroup = 13 | Lamentamos que não possa redefinir a sua palavra-passe neste momento porque o seu administrador não configura a sua conta para utilizar o reset da palavra-passe. Não há mais nenhuma ação que possa tomar para resolver esta situação. Por favor contacte o seu administrador e peça-lhes para configurar a sua conta para a redefinição da palavra-passe.<br /><br />Para saber mais sobre a configuração da conta para o reset da palavra-passe, consulte [o reset da palavra-passe para os utilizadores](./howto-sspr-deployment.md). | SSPR_0013: Não é membro de um grupo ativado para reset de palavra-passe. Contacte o seu administrador e solicite a adição ao grupo. |
| UtilizadorNotProperly Configurado = 14 | Lamentamos, não pode redefinir a sua senha neste momento porque faltam informações necessárias na sua conta. Não há mais nenhuma ação que possa tomar para resolver esta situação. Por favor contacte-o e peça-lhes para redefinirem a sua palavra-passe. Depois de ter acesso à sua conta novamente, tem de registar as informações necessárias.<br /><br />Para registar informações, siga os passos no [Registo para o artigo de redefinição da palavra-passe de autosserviço.](../user-help/active-directory-passwords-reset-register.md) | SSPR_0014: São necessárias informações adicionais de segurança para redefinir a sua palavra-passe. Para prosseguir, contacte o seu administrador e peça-lhes que repuem a sua palavra-passe. Depois de ter acesso à sua conta, pode registar informações adicionais de segurança em https://aka.ms/ssprsetup . O seu administrador pode adicionar informações de segurança adicionais à sua conta seguindo os passos definidos [e ler dados de autenticação para reset de senha.](howto-sspr-authenticationdata.md) |
| OnPremisesAdminActionRequired = 29 | Lamentamos, não podemos redefinir a sua palavra-passe neste momento devido a um problema com a configuração de reset de password da sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Por favor contacte o seu administrador e peça-lhes para investigarem. <br /><br />Ou<br /><br />Não podemos redefinir a sua palavra-passe neste momento devido a um problema com a configuração de reset de palavra-passe da sua organização. Não há mais nenhuma ação que possa tomar para resolver esta questão. Por favor contacte o seu administrador e peça-lhes para investigarem.<br /><br />Para saber mais sobre o problema potencial, consulte a [gravação da palavra-passe de resolução de problemas](troubleshoot-sspr-writeback.md). | SSPR_0029: Não conseguimos redefinir a sua palavra-passe devido a um erro na configuração do seu local. Por favor contacte o seu administrador e peça-lhes para investigarem. |
| OnPremisesConnectivityError = 30 | Lamentamos, não podemos redefinir a sua senha neste momento por causa de problemas de conectividade com a sua organização. Neste momento, não há ação a tomar, mas o problema pode ser resolvido se tentarmos mais tarde. Se o problema persistir, contacte o seu administrador e peça-lhes que investiguem.<br /><br />Para saber mais sobre problemas de conectividade, consulte [a conectividade de writeback de palavras-passe de resolução de problemas](troubleshoot-sspr-writeback.md). | SSPR_0030: Não podemos redefinir a sua palavra-passe devido a uma má ligação com o seu ambiente no local. Contacte o seu administrador e peça-lhes que investiguem.|

## <a name="azure-ad-forums"></a>Fóruns da AD de Azure

Se tiver perguntas gerais sobre o Azure AD e o reset da palavra-passe de autosserviço, pode pedir assistência à comunidade na página de perguntas do Microsoft Q&A para o [Azure Ative Directory](/answers/topics/azure-active-directory.html). Os membros da comunidade incluem engenheiros, gestores de produtos, MVPs e outros profissionais de TI.

## <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Se não encontrar a resposta para um problema, as nossas equipas de apoio estão sempre disponíveis para o ajudar mais.

Para ajudá-lo corretamente, pedimos que forneça o máximo de detalhes possível ao abrir um caso. Estes detalhes incluem os seguintes:

* **Descrição geral do erro:** Qual é o erro? Qual foi o comportamento que foi notado? Como podemos reproduzir o erro? Forneça o máximo de detalhes possível.
* **Página**: Em que página estava quando reparou no erro? Inclua o URL se for capaz e uma imagem da página.
* **Código de suporte**: Qual foi o código de suporte que foi gerado quando o utilizador viu o erro?
   * Para encontrar este código, reproduza o erro, selecione a ligação **de código de suporte** na parte inferior do ecrã e envie ao engenheiro de suporte o GUIADOr que resulta.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="O código de suporte está localizado no canto inferior direito da janela do navegador web.":::

  * Se estiver numa página sem um código de suporte na parte inferior, selecione F12 e procure o SID e o CID e envie esses dois resultados para o engenheiro de suporte.
* **Data, hora e fuso horário**: Inclua a data e a hora *exatas com o fuso horário* em que ocorreu o erro.
* **ID do utilizador**: Quem foi o utilizador que viu o erro? Um exemplo é * \@ o contoso.com do utilizador*.
   * É um utilizador federado?
   * É um utilizador de autenticação pass-through?
   * É um utilizador sincronizado com palavra-passe?
   * É um utilizador só de nuvens?
* **Licenciamento**: O utilizador tem uma licença AD Azure atribuída?
* **Registo do evento**de aplicação : Se estiver a utilizar a gravação de palavra-passe e o erro estiver na sua infraestrutura no local, inclua uma cópia zipped do registo do evento da sua aplicação a partir do servidor Azure AD Connect.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a SSPR, veja [como funciona: Azure AD autosserviço de autosserviço reset](concept-sspr-howitworks.md) ou [Como é que a palavra-passe de autosserviço repõe o funcionamento em Azure AD?](concept-sspr-writeback.md)
