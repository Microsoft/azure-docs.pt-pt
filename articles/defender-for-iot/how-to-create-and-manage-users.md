---
title: Criar e gerir utilizadores
description: Criar e gerir os utilizadores de sensores e a consola de gestão no local. Os utilizadores podem ser atribuídos ao papel de administrador, analista de segurança ou utilizador apenas de leitura.
ms.date: 03/03/2021
ms.topic: article
ms.openlocfilehash: 2afc3cb2b9cfc0ac6b75c98198d9f0965b6dc04c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779023"
---
# <a name="about-defender-for-iot-console-users"></a>Sobre o Defender para utilizadores de consolas IoT

Este artigo descreve como criar e gerir utilizadores de sensores e da consola de gestão no local. As funções do utilizador incluem administrador, analista de segurança ou utilizador apenas de leitura. Cada função está associada a uma gama de permissões a ferramentas para o sensor ou consola de gestão no local. As funções são concebidas para facilitar o acesso granular e seguro ao Azure Defender para IoT.

As funcionalidades também estão disponíveis para acompanhar a atividade do utilizador e ativar o registo do Ative Directory.

Por predefinição, cada consola de gestão de sensores e instalações é instalada com um *utilizador de ciberx e suporte.* Estes utilizadores têm acesso a ferramentas avançadas para resolução e configuração de problemas. Os utilizadores de administradores devem iniciar sessão com estas credenciais de utilizador, criar um utilizador administrativo e, em seguida, criar utilizadores extra para analistas de segurança e utilizadores apenas de leitura.

## <a name="role-based-permissions"></a>Permissões baseadas em funções
Estão disponíveis as seguintes funções de utilizador:

- **Leia apenas:** Os utilizadores apenas para leitura executam tarefas como alertas de visualização e dispositivos no mapa do dispositivo. Estes utilizadores têm acesso às opções exibidas no **âmbito da Navegação.**

- **Analista de** segurança : Os analistas de segurança têm permissões de utilizadores apenas de leitura. Também podem realizar ações em dispositivos, reconhecer alertas e usar ferramentas de investigação. Estes utilizadores têm acesso às opções exibidas no âmbito **da Navegação** e **Análise.**

- **Administrador**: Os administradores têm acesso a todas as ferramentas, incluindo a definição de configurações do sistema, a criação e gestão de utilizadores, entre outras. Estes utilizadores têm acesso às opções exibidas em **Navegação,** **Análise** e **Administração.**

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>Permissões baseadas em funções para ferramentas de consola de gestão de empresas no local

Esta secção descreve permissões disponíveis para administradores, analistas de segurança e utilizadores apenas de leitura para a consola de gestão no local.  

| Permissão | Só de leitura | Analista de segurança | Administrador |
|--|--|--|--|
| Ver e filtrar o mapa da empresa | ✓ | ✓ | ✓ |
| Construir um site |  |  | ✓ |
| Gerir um site (adicionar e editar zonas) |  |  | ✓ |
| Inventário de dispositivos de visualização e filtro | ✓ | ✓ | ✓ |
| Ver e gerir alertas: reconhecer, aprender e pin | ✓ | ✓ | ✓ |
| Gerar relatórios |  | ✓ | ✓ |
| Ver relatórios de avaliação de riscos |  | ✓ | ✓ |
| Definir exclusões de alerta |  | ✓ | ✓ |
| Ver ou definir grupos de acesso |  |  | ✓ |
| Gerir as definições do sistema |  |  | ✓ |
| Gerir utilizadores |  |  | ✓ |
| Enviar dados de alerta aos parceiros |  |  | ✓ |
| Gerir certificados |  |  | ✓ |
| Tempo limite de sessão quando os utilizadores não estão ativos | 30 minutos | 30 minutos  | 30 minutos  |

#### <a name="assign-users-to-access-groups"></a>Atribuir aos utilizadores o acesso a grupos

Os administradores podem melhorar o controlo de acesso do utilizador no Defender para IoT, atribuindo os utilizadores a grupos de *acesso específicos*. Os grupos de acesso são atribuídos a zonas, locais, regiões e unidades de negócio onde está localizado um sensor. Ao atribuir os utilizadores a grupos de acesso, os administradores ganham controlo específico sobre onde os utilizadores gerem e analisam as deteções de dispositivos. 

Trabalhar desta forma acomoda grandes organizações onde as permissões dos utilizadores podem ser complexas ou determinadas por uma política global de segurança organizacional. Para obter mais informações, consulte [Definir o controlo global de acesso.](how-to-define-global-user-access-control.md)

### <a name="role-based-permissions-to-sensor-tools"></a>Permissões baseadas em funções para ferramentas sensoriais

Esta secção descreve permissões disponíveis para administradores de sensores, analistas de segurança e utilizadores apenas de leitura.  

| Permissão | Só de leitura | Analista de segurança | Administrador |
|--|--|--|--|
| Ver o dashboard | ✓ | ✓ | ✓ |
| Vistas de zoom do mapa de controlo |  |  | ✓ |
| Ver alertas | ✓ | ✓ | ✓ |
| Gerir alertas: reconhecer, aprender e pin |  | ✓ | ✓ |
| Ver eventos numa linha do tempo |  | ✓ | ✓ |
| Dispositivos autorizados, dispositivos de digitalização conhecidos, dispositivos de programação |  | ✓ | ✓ |
| Ver dados da investigação | ✓ | ✓ | ✓ |
| Gerir as definições do sistema |  |  | ✓ |
| Gerir utilizadores |  |  | ✓ |
| Servidores DNS para pesquisa inversa |  |  | ✓ |
| Enviar dados de alerta aos parceiros |  | ✓ | ✓ |
| Criar comentários de alerta |  | ✓ | ✓ |
| Ver programação mudar história | ✓ | ✓ | ✓ |
| Criar regras de alerta personalizadas |  | ✓ | ✓ |
| Gerir várias notificações simultaneamente |  | ✓ | ✓ |
| Gerir certificados |  |  | ✓ |
| Tempo limite de sessão quando os utilizadores não estão ativos | 30 minutos | 30 minutos | 30 minutos |

## <a name="define-users"></a>Definir utilizadores

Esta secção descreve como definir os utilizadores. Os utilizadores de Cyberx, suporte e administrador podem adicionar, remover e atualizar outras definições do utilizador.

Para definir um utilizador:

1. A partir do painel esquerdo para o sensor ou para a consola de gestão no local, selecione **Utilizadores**.
1. Na janela **do Utilizador,** selecione **Criar Utilizador**.
1. No painel **de utilização** Criar, defina os seguintes parâmetros:

   - **Nome de utilizador**: Introduza um nome de utilizador.
   - **Email**: Insira o endereço de e-mail do utilizador.
   - **Primeiro nome:** Introduza o primeiro nome do utilizador.
   - **Último Nome**: Introduza o apelido do utilizador.
   - **Função**: Definir o papel do utilizador. Consulte [permissões baseadas em funções.](#role-based-permissions)
   - **Grupo de Acesso**: Se estiver a criar um utilizador para a consola de gestão no local, defina o grupo de acesso do utilizador. Ver [Definir controlo global de acesso.](how-to-define-global-user-access-control.md)
   - **Palavra-passe**: Selecione o tipo de utilizador da seguinte forma:
     - **Utilizador Local**: Defina uma palavra-passe para o utilizador de um sensor ou de uma consola de gestão no local. A palavra-passe deve incluir pelo menos seis caracteres e deve incluir letras e números.
     - **Utilizador do Diretório Ativo**: Pode permitir que os utilizadores entrem no sensor ou na consola de gestão utilizando credenciais de Ative Directory. Os grupos de Diretório Ativo definidos podem ser associados a níveis de permissão específicos. Por exemplo, configurar um grupo de Diretório Ativo específico e atribuir todos os utilizadores do grupo ao tipo de utilizador apenas de leitura.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="Gerencie os seus utilizadores.":::

## <a name="user-session-timeout"></a>Tempo limite de sessão do utilizador

Se os utilizadores não estiverem ativos no teclado ou no rato por um momento específico, são assinados fora da sessão e devem iniciar sessão novamente.

Quando os utilizadores não trabalharam com o rato ou teclado da consola durante um período de 30 minutos, é forçado a uma sessão de sessão.

Esta funcionalidade é ativada por padrão e por atualização, mas pode ser desativada. Além disso, os tempos de contagem da sessão podem ser atualizados. Os tempos de sessão são definidos em segundos. As definições são aplicadas por sensor e consola de gestão no local.

Uma mensagem de tempo limite de sessão aparece na consola quando o tempo limite de inatividade passou.

### <a name="control-inactivity-sign-out"></a>Controlo da inatividade

Os utilizadores de administradores podem ativar e desativar a inatividade e ajustar os limiares de inatividade.

Para aceder ao comando:

1. Inscreva-se no CLI para a consola de gestão de sensores ou no local utilizando o Defender para obter credenciais administrativas IoT.

1. Introduza `sudo nano /var/cyberx/properties/authentication`.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

Para desativar a funcionalidade, mude `infinity_session_expiration = true` para `infinity_session_expiration = false` .

Para atualizar os períodos de contagem de assinaturas, ajuste o `= <number>` valor ao tempo necessário.

## <a name="track-user-activity"></a>Acompanhar a atividade do utilizador 

Pode rastrear a atividade do utilizador na linha temporal do evento em cada sensor. A linha de tempo exibe o evento ou o dispositivo afetado, e a hora e a data em que o utilizador realizou a atividade.

Para visualizar a atividade do utilizador:

1. Inscreva-se no sensor.
1. Na linha temporal do evento, ative a opção **Operações de Utilização.** 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="Ver a atividade de um utilizador.":::

## <a name="integrate-with-active-directory-servers"></a>Integre-se com servidores ative directory 

Configure o sensor ou a consola de gestão no local para trabalhar com o Ative Directory. Isto permite que os utilizadores do Ative Directory acedam ao Defender para consolas IoT utilizando as suas credenciais de Ative Directory.

São suportados dois tipos de autenticação baseada em LDAP:

- **Autenticação completa**: Os dados do utilizador são recuperados a partir do servidor LDAP. Exemplos são o primeiro nome, apelido, e-mail e permissões do utilizador.

- **Utilizador de confiança**: Apenas a palavra-passe do utilizador é recuperada. Outros detalhes do utilizador que são recuperados são baseados nos utilizadores definidos no sensor.

### <a name="active-directory-and-defender-for-iot-permissions"></a>Diretório Ativo e Defender para permissões IoT

Pode associar grupos de Diretório Ativo definidos aqui com níveis de permissão específicos. Por exemplo, configurar um grupo específico de Diretório Ativo e atribuir permissões de Read Only a todos os utilizadores do grupo.

Para configurar o Diretório Ativo:

1. A partir do painel esquerdo, selecione **Definições do Sistema**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Veja as definições do sistema Ative Directory.":::

2. No painel **de definições** do sistema, selecione **Ative Directory**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Edite as configurações do Ative Directory.":::

3. Na caixa de diálogo de **configuração de configuração do diretório ativo de edição,** selecione **Ative Directory Integration Enabled**  >  **Save**. A caixa de diálogo de **configuração de diretório ativo** de edição expande-se e agora pode introduzir os parâmetros para configurar o Ative Directory.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Introduza os parâmetros para configurar o Ative Directory.":::

    > [!NOTE]
    > - Deve definir os parâmetros LDAP aqui exatamente como aparecem no Ative Directory.
    > - Para todos os parâmetros do Diretório Ativo, utilize apenas minúsculas. Utilize minúsculas mesmo quando as configurações no Ative Directory utilizarem maiúsculas.
    > - Não é possível configurar tanto lDAP como LDAPS para o mesmo domínio. No entanto, pode utilizar ambos para diferentes domínios ao mesmo tempo.

4. Desa esta medida os parâmetros do servidor ative directory, da seguinte forma:

   | Parâmetro do servidor | Description |
   |--|--|
   | Controlador de domínio FQDN | Desaceie o nome de domínio totalmente qualificado (FQDN) exatamente como aparece no seu servidor LDAP. Por exemplo, introduza `host1.subdomain.domain.com`. |
   | Porta controladora de domínio | Defina a porta na qual o seu LDAP está configurado. |
   | Domínio primário | Desaça o nome de domínio (por exemplo) `subdomain.domain.com` e o tipo de ligação de acordo com a configuração LDAP. |
   | Grupos de diretório ativo | Introduza os nomes de grupo definidos na configuração do Ative Directory no servidor LDAP. |
   | Domínios fidedignos | Para adicionar um domínio de confiança, adicione o nome de domínio e o tipo de ligação de um domínio fidedigno. <br />Só é possível configurar domínios fidedignos para utilizadores definidos pelos utilizadores. |

#### <a name="activedirectory-groups-for-the-on-premises-management-console"></a>Grupos ActiveDirectory para a consola de gestão on-in

Se estiver a criar grupos de Diretório Ativo para utilizadores de consolas de gestão no local, tem de criar uma regra do Grupo de Acesso para cada grupo ative. As credenciais de gestão de gestão no local não funcionarão se não existir uma regra do Grupo de Acesso para o grupo de utilizadores do Ative Directory. Ver [Definir controlo global de acesso.](how-to-define-global-user-access-control.md)

1. Selecione **Guardar**.

2. Para adicionar um servidor de confiança, **selecione Adicionar Servidor** e configuure outro servidor.

## <a name="resetting-passwords"></a>Redefinir palavras-passe

### <a name="cyberx-or-support-user"></a>Utilizador de CyberX ou Suporte

Apenas o utilizador **CyberX** e **Suporte** tem acesso à funcionalidade **de recuperação de passwords.** Se o utilizador **CyberX** ou **Support** se esqueceu da sua palavra-passe, pode ser reposta a palavra-passe através da opção **de recuperação** de passwords na página de entrada do Defender para ioT.

Para redefinir a palavra-passe para um utilizador CyberX ou Support:

1. No ecrã de entrada de IoT do Defender, selecione  **a recuperação da palavra-passe**. O ecrã **de recuperação da palavra-passe** abre.

1. Selecione **CyberX** ou **Support**, e copie o identificador único.

1. Navegue até ao portal Azure e selecione **Sites e Sensores.**  

1. Selecione o ícone do Filtro de **Subscrição** :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false":::  a partir da barra de ferramentas superior e selecione a subscrição a que o seu sensor está ligado.

1. Selecione o **separador palavra-passe da consola de gestão recuperar no local.**

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="Selecione o botão de gestão de recuperação no local para descarregar o ficheiro de recuperação.":::

1. Introduza o identificador único que recebeu no ecrã de recuperação da **Palavra-passe** e selecione **Recuperar**. O `password_recovery.zip` ficheiro foi descarregado.

    > [!NOTE]
    > Não altere o ficheiro de recuperação da palavra-passe. É um ficheiro assinado e não funcionará se o adulterares.

1. No ecrã de recuperação da **palavra-passe,** selecione **Upload**. A janela **do Ficheiro de Recuperação de Passwords do Upload** será aberta.

1. **Selecione Procurar** para localizar o seu `password_recovery.zip` ficheiro ou arrastar o para a `password_recovery.zip` janela.

    > [!NOTE]
    > Pode aparecer uma mensagem de erro indicando que o ficheiro é inválido. Para corrigir esta mensagem de erro, certifique-se de que selecionou a subscrição certa antes de descarregar `password_recovery.zip` e descarregá-la novamente.  

1. Selecione **Next**, e o seu utilizador, e a palavra-passe gerada pelo sistema para a sua consola de gestão aparecerá então.

### <a name="administrator-security-analyst-and-read-only-user"></a>Administrador, analista de segurança e só utilizador de leitura

Leia apenas e os analistas de Segurança não podem redefinir a sua própria palavra-passe e precisam de contactar um utilizador com as funções de Administrador, Suporte ou CyberX, a fim de redefinir a sua palavra-passe. Um utilizador do Administrador deve contactar o utilizador **CyberX** ou **Support** para redefinir a sua palavra-passe.

Para redefinir a palavra-passe de um utilizador no Sensor:

1. Um administrador, suporte ou utilizador da função CyberX deve iniciar sposição no sensor.

1. Selecione **Utilizadores** do painel à esquerda.

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="Selecione a opção de utilizador a partir do painel lateral esquerdo.":::

1. Localize o utilizador e **selecione Editar** a partir do menu de retirada **de Ações.**

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="selecione editar a partir do menu de ações dropdown.":::

1. Introduza a nova palavra-passe nos campos **'Nova Palavra-passe'** e confirme novos campos **de palavra-passe.**

1. Selecione **Atualizar**.

Para redefinir a palavra-passe de um utilizador na consola de gestão no local:

1. Um administrador, suporte ou utilizador da função CyberX deve iniciar sposição no sensor.

1. Selecione **Utilizadores** do painel à esquerda.

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="No painel esquerdo selecione a opção do utilizador.":::

1. Localize o seu utilizador e selecione o ícone de edição :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false"::: .

1. Introduza a nova palavra-passe nos campos **'Nova Palavra-passe'** e confirme novos campos **de palavra-passe.**

1. Selecione **Atualizar**.

## <a name="see-also"></a>Ver também

[Ative e crie o seu sensor](how-to-activate-and-set-up-your-sensor.md) 
 [Ative e crie a sua consola](how-to-activate-and-set-up-your-on-premises-management-console.md) 
 de gestão no local [Rastrear atividade de sensor](how-to-track-sensor-activity.md)
