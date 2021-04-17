---
title: Como gerir administradores locais em dispositivos aderidos a Azure AD
description: Saiba como atribuir funções Azure ao grupo de administradores locais de um dispositivo Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 806ff92fcf75ff8d1c8e092d7ff4435751a9e7db
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529900"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Como gerir o grupo de administradores locais em Azure AD juntou dispositivos

Para gerir um dispositivo Windows, tem de ser membro do grupo de administradores locais. Como parte do processo de adesão do Azure Ative Directory (Azure AD), a Azure AD atualiza a adesão deste grupo a um dispositivo. Pode personalizar a atualização de membros para satisfazer os requisitos do seu negócio. Uma atualização de adesão é, por exemplo, útil se pretender que o seu pessoal de helpdesk faça tarefas que exijam direitos de administrador num dispositivo.

Este artigo explica como funciona a atualização de membros dos administradores locais e como pode personalizá-la durante uma AZure AD Join. O conteúdo deste artigo não se aplica a um híbrido **Azure AD.**

## <a name="how-it-works"></a>Como funciona

Quando liga um dispositivo Windows ao AD Azure utilizando uma junta AZure AD, a Azure AD adiciona os seguintes princípios de segurança ao grupo de administradores locais no dispositivo:

- O papel de administrador global da AZure AD
- A função de administrador de dispositivo AZure AD 
- O utilizador que executa a ad AD Azure junta-se   

Ao adicionar funções AD AZure ao grupo de administradores locais, pode atualizar os utilizadores que podem gerir um dispositivo a qualquer momento em AZure AD sem modificar nada no dispositivo. A Azure AD também adiciona o papel de administrador de dispositivo AZure AD ao grupo de administradores locais para apoiar o princípio do menor privilégio (PoLP). Além dos administradores globais, também é possível permitir aos utilizadores que *apenas* tenham sido designados para gerir um dispositivo. 

## <a name="manage-the-global-administrators-role"></a>Gerir o papel de administradores globais

Para visualizar e atualizar a adesão à função de administrador global, consulte:

- [Ver todos os membros de uma função de administrador no Azure Ative Directory](../roles/manage-roles-portal.md)
- [Atribua um utilizador a funções de administrador no Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Gerir a função de administrador de dispositivos 

No portal Azure, pode gerir a função de administrador do dispositivo na página **Dispositivos.** Para abrir a página **dispositivos:**

1. Inscreva-se no seu [portal Azure](https://portal.azure.com) como administrador global.
1. Procure e selecione *Azure Active Directory*.
1. Na secção **Gerir,** clique em **Dispositivos**.
1. Na página **dispositivos,** clique nas **definições do Dispositivo**.

Para modificar a função de administrador do dispositivo, configurar **administradores locais adicionais em dispositivos ligados a Azure AD**.  

![Administradores locais adicionais](./media/assign-local-admin/10.png)

>[!NOTE]
> Esta opção requer um inquilino Azure AD Premium. 

Os administradores do dispositivo são atribuídos a todos os dispositivos ad AZure. Não é possível colocar os administradores do dispositivo num conjunto específico de dispositivos. A atualização da função de administrador do dispositivo não tem necessariamente um impacto imediato nos utilizadores afetados. Nos dispositivos em que um utilizador já está inscrito, a elevação do privilégio ocorre quando *ambas* as ações abaixo acontecem:

- Passaram-se até 4 horas para a Azure AD emitir um novo Token Primary Refresh com os privilégios apropriados. 
- O utilizador assina e assina de volta, não bloqueia/desbloqueia, para refrescar o seu perfil.

>[!NOTE]
> As ações acima referidas não são aplicáveis aos utilizadores que não tenham assinado anteriormente no dispositivo relevante. Neste caso, os privilégios do administrador são aplicados imediatamente após a sua primeira sing-in ao dispositivo. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Gerir privilégios de administrador utilizando grupos Azure AD (pré-visualização)

A partir da versão 10 do Windows 10, pode utilizar grupos AD AZure para gerir privilégios de administrador em dispositivos aderidos a Azure AD com a política de MDM [de Grupos Restritos.](/windows/client-management/mdm/policy-csp-restrictedgroups) Esta política permite-lhe atribuir utilizadores individuais ou grupos AD AD ao grupo de administradores locais num dispositivo aderido a Azure AD, proporcionando-lhe a granularidade para configurar administradores distintos para diferentes grupos de dispositivos. 

>[!NOTE]
> A partir da atualização do Windows 10 20H2, recomendamos a utilização da política [de Utilizadores e Grupos Locais](/windows/client-management/mdm/policy-csp-localusersandgroups) em vez da política de Grupos Restritos


Atualmente, não existe UI no Intune para gerir estas políticas e elas precisam de ser configuradas usando [configurações personalizadas de OMA-URI](/mem/intune/configuration/custom-settings-windows-10). Algumas considerações para a utilização de qualquer uma destas políticas: 

- A adição de grupos AD Azure através da política requer o SID do grupo que pode ser obtido executando a API do [Gráfico microsoft para grupos](/graph/api/resources/group). O SID é definido pelo imóvel `securityIdentifier` na resposta da API.
- Quando a política dos Grupos Restritos é aplicada, qualquer membro atual do grupo que não esteja na lista de membros é removido. Assim, a aplicação desta política com novos membros ou grupos removerá os administradores existentes, nomeadamente o utilizador que aderiu ao dispositivo, a função de administrador do Dispositivo e a função de administrador global do dispositivo. Para evitar a remoção dos membros existentes, é necessário configurá-los como parte da lista de membros da política dos Grupos Restritos. Esta limitação é abordada se utilizar a política de Utilizadores e Grupos Locais que permite atualizações incrementais para a adesão ao grupo
- Os privilégios de administrador que utilizam ambas as políticas são avaliados apenas para os seguintes grupos bem conhecidos num dispositivo Windows 10 - Administradores, Utilizadores, Convidados, Utilizadores de Energia, Utilizadores remotos de Desktop e Utilizadores de Gestão Remota. 
- A gestão de administradores locais que utilizam grupos AD Azure não é aplicável aos dispositivos AD Azure híbridos ou a dispositivos registados Azure AD.
- Embora a política de Grupos Restritos existisse antes da versão 2004 do Windows 10, não suportava grupos AD AD como membros do grupo de administradores locais de um dispositivo. 

## <a name="manage-regular-users"></a>Gerir utilizadores regulares

Por predefinição, o Azure AD adiciona ao utilizador que executa a AD AZure junta-se ao grupo de administrador no dispositivo. Se quiser evitar que os utilizadores regulares se tornem administradores locais, tem as seguintes opções:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) - O Windows Autopilot oferece-lhe uma opção para evitar que o utilizador primário realize a junção de administrador local. Pode fazê-lo [criando um perfil autopiloto.](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)
- [Inscrição a granel](/intune/windows-bulk-enroll) - Uma junção AD Azure que é realizada no contexto de uma inscrição a granel acontece no contexto de um utilizador criado automaticamente. Os utilizadores que se inscrevam após a adesão de um dispositivo não são adicionados ao grupo de administradores.   

## <a name="manually-elevate-a-user-on-a-device"></a>Elevar manualmente um utilizador num dispositivo 

Além de utilizar o processo de aderimento Azure AD, também pode elevar manualmente um utilizador regular para se tornar um administrador local num dispositivo específico. Este passo requer que você já seja membro do grupo de administradores locais. 

A partir da versão do **Windows 10 1709,** pode executar esta tarefa a partir de **Definições -> Contas -> Outros utilizadores**. **Selecione Adicionar um utilizador de trabalho ou escola,** introduzir a UPN do utilizador na conta do **utilizador** e selecionar *administrador* sob o tipo **de Conta**  
 
Além disso, também pode adicionar utilizadores usando a solicitação de comando:

- Se os seus utilizadores inquilinos estiverem sincronizados a partir do Ative Directory, utilize `net localgroup administrators /add "Contoso\username"` .
- Se os seus utilizadores inquilinos forem criados em Azure AD, use `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Considerações 

Não é possível atribuir grupos à função de administrador do dispositivo, apenas são permitidos utilizadores individuais.

Os administradores do dispositivo são atribuídos a todos os dispositivos Azure AD. Não podem ser examinados para um conjunto específico de dispositivos.

Quando retira os utilizadores da função de administrador do dispositivo, eles ainda têm o privilégio de administrador local num dispositivo, desde que estejam inscritos no mesmo. O privilégio é revogado durante a sua próxima entrada quando um novo token de atualização primária é emitido. Esta revogação, semelhante à elevação do privilégio, pode demorar até 4 horas.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma descrição geral sobre como gerir dispositivos no portal do Azure, veja [Managing devices using the Azure portal](device-management-azure-portal.md) (Gerir dispositivos no portal do Azure)
- Para saber mais sobre o Acesso Condicional baseado no dispositivo, consulte [as políticas de acesso condicional do Azure Ative Com base em dispositivos](../conditional-access/require-managed-devices.md).
