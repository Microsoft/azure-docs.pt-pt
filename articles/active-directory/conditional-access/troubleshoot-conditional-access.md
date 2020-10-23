---
title: Problemas de resolução de problemas com acesso condicional - Diretório Ativo Azure
description: Este artigo descreve o que fazer quando as suas políticas de Acesso Condicional resultam em resultados inesperados
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12f722977329bd5d79d4d0e410a29c730faf00c5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92145091"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Resolver problemas de início de sessão com Acesso Condicional

As informações deste artigo podem ser usadas para resolver resultados inesperados de login relacionados com o Acesso Condicional usando mensagens de erro e registos de logins AD Azure.

## <a name="select-all-consequences"></a>Selecione "todas" as consequências

A estrutura de Acesso Condicional proporciona-lhe uma grande flexibilidade de configuração. No entanto, uma grande flexibilidade também significa que deve rever cuidadosamente cada política de configuração antes de a lançar para evitar resultados indesejáveis. Neste contexto, deve prestar especial atenção às atribuições que afetam conjuntos completos, tais como **todos os utilizadores /grupos /apps cloud**.

As organizações devem evitar as seguintes configurações:

**Para todos os utilizadores, todas as aplicações na nuvem:**

- **Bloquear o acesso** - Esta configuração bloqueia toda a sua organização.
- **Exigir** que o dispositivo seja marcado como conforme - Para os utilizadores que ainda não tenham inscrito os seus dispositivos, esta política bloqueia todo o acesso, incluindo o acesso ao portal Intune. Se é um administrador sem um dispositivo matriculado, esta política impede-o de voltar ao portal Azure para alterar a política.
- **Requer dispositivo de domínio Híbrido Azure AD -** Este acesso ao bloco de política também tem o potencial de bloquear o acesso a todos os utilizadores da sua organização se não tiverem um dispositivo híbrido Azure AD.
- **Requer uma política de proteção de aplicações** - Este acesso por bloqueio de políticas também tem o potencial de bloquear o acesso a todos os utilizadores da sua organização se não tiver uma política Intune. Se é um administrador sem uma aplicação de cliente que tenha uma política de proteção de aplicações Intune, esta política impede-o de voltar a portais como o Intune e o Azure.

**Para todos os utilizadores, todas as aplicações em nuvem, todas as plataformas do dispositivo:**

- **Bloquear o acesso** - Esta configuração bloqueia toda a sua organização.

## <a name="conditional-access-sign-in-interrupt"></a>Interrupção de acesso condicional

A primeira maneira é rever a mensagem de erro que aparece. Para problemas de sessão ao utilizar um navegador web, a própria página de erros tem informações detalhadas. Só esta informação pode descrever qual é o problema e isso pode sugerir uma solução.

![Iniciar em erro - dispositivo compatível necessário](./media/troubleshoot-conditional-access/image1.png)

No erro acima referido, a mensagem afirma que a aplicação só pode ser acedida a partir de dispositivos ou aplicações de clientes que satisfaçam a política de gestão de dispositivos móveis da empresa. Neste caso, a aplicação e o dispositivo não cumprem essa política.

## <a name="azure-ad-sign-in-events"></a>Eventos de entrada em Ad AD Azure

O segundo método para obter informações detalhadas sobre a interrupção do sinal é rever os eventos de inscrição Azure AD para ver qual a política ou políticas de acesso condicional e porquê.

Mais informações podem ser encontradas sobre o problema clicando em **Mais Detalhes** na página inicial de erro. Clicar em **mais detalhes** revelará informações de resolução de problemas que são úteis ao pesquisar os eventos de sessão de ad AD do Azure para o evento de falha específica que o utilizador viu ou ao abrir um incidente de suporte com a Microsoft.

![Mais detalhes de um Acesso Condicional interrompeu o acesso ao navegador web.](./media/troubleshoot-conditional-access/image2.png)

Para saber qual a política ou políticas de acesso condicional aplicadas e por que fazer o seguinte.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou leitor global.
1. Navegue até a **Azure Ative Directory**  >  **Sign-ins**.
1. Encontre o evento para a revisão do s-in. Adicione ou remova filtros e colunas para filtrar informações desnecessárias.
   1. Adicione filtros para reduzir o âmbito:
      1. **Identificação de correlação** quando tiver um evento específico para investigar.
      1. **Acesso condicional** para ver falhas políticas e sucesso. Procure o filtro para mostrar apenas falhas na limitação dos resultados.
      1. **Nome de utilizador** para ver informações relacionadas com utilizadores específicos.
      1. **Data** marcada para o prazo em questão.

   ![Selecionando o filtro de acesso condicional no registo de entradas](./media/troubleshoot-conditional-access/image3.png)

1. Uma vez encontrada a inscrição no caso de corresponder à falha de inscrição do utilizador, selecione o separador **Acesso Condicional.** O separador acesso condicional mostrará a política ou políticas específicas que resultaram na interrupção do acesso.
   1. As informações no **separador de resolução de problemas e suporte** podem fornecer uma razão clara para que uma sessão falhou, tal como um dispositivo que não cumpriu os requisitos de conformidade.
   1. Para investigar mais aprofundadamente, aprofundar a configuração das políticas clicando no **Nome de Política**. Clicar no **Nome de Política** mostrará a interface de utilizador de configuração de política para a política selecionada para revisão e edição.
   1. Os **dados do** **utilizador** e do dispositivo que foram utilizados para a avaliação da política de acesso condicional também estão disponíveis nos separadores **de Informação Básica,** **Localização,** **Informação do Dispositivo,** Detalhes de **Autenticação**e **Detalhes Adicionais** do evento de inscrição.

### <a name="policy-details"></a>Detalhes da política

A seleção da elipse do lado direito da política num evento de inscrição traz detalhes políticos. Isto dá aos administradores informações adicionais sobre a razão pela qual uma política foi aplicada com sucesso ou não.

   ![Assine no separador de acesso condicional do evento](./media/troubleshoot-conditional-access/image5.png)

   ![Detalhes da política (pré-visualização)](./media/troubleshoot-conditional-access/policy-details.png)

O lado esquerdo fornece detalhes recolhidos no início de saúde e o lado direito fornece detalhes sobre se esses detalhes satisfazem os requisitos das políticas de Acesso Condicional aplicadas. As políticas de acesso condicional só se aplicam quando todas as condições estão preenchidas ou não configuradas.

Se a informação no evento não for suficiente para entender os resultados de inscrição ou ajustar a política para obter os resultados desejados, então um incidente de apoio pode ser aberto. Navegue no separador de **resolução e suporte** de problemas do evento e selecione Criar um novo pedido de **suporte**.

![O separador de resolução de problemas e suporte do evento de inscrição](./media/troubleshoot-conditional-access/image6.png)

Ao submeter o incidente, forneça o ID do pedido e a hora e data do evento de inscrição nos detalhes de submissão do incidente. Estas informações permitirão que o suporte da Microsoft encontre o evento que lhe preocupa.

### <a name="conditional-access-error-codes"></a>Códigos de erro de acesso condicional

| Código de erro de inscrição | Cadeia de erro |
| --- | --- |
| 53000 | DispositivoNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | AplicaçãoUsedIsNotAnApprovedAppedAppedAppedAppedApped |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="what-to-do-if-you-are-locked-out-of-the-azure-portal"></a>O que fazer se estiver trancado fora do portal Azure?

Se estiver bloqueado fora do portal Azure devido a uma definição incorreta numa política de acesso condicional:

- Verifique se há outros administradores na sua organização que ainda não estão bloqueados. Um administrador com acesso ao portal Azure pode desativar a política que está a afetar a sua inscrição. 
- Se nenhum dos administradores da sua organização conseguir atualizar a apólice, envie um pedido de apoio. O suporte da Microsoft pode rever e após a confirmação atualizar as políticas de Acesso Condicional que estão a impedir o acesso.

## <a name="next-steps"></a>Passos seguintes

- [Relatórios de atividade de início de sessão no portal do Azure Active Directory](../reports-monitoring/concept-sign-ins.md)
- [Resolução de problemas Acesso Condicional usando a ferramenta E Se](troubleshoot-conditional-access-what-if.md)
