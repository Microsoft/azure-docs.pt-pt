---
title: Problemas de resolução de problemas com acesso condicional - Diretório Ativo Azure
description: Este artigo descreve o que fazer quando as suas políticas de Acesso Condicional resultam em resultados inesperados
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6778b556795f4e079100f1a7bcbb8b9465e9e315
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88032973"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Resolver problemas de início de sessão com Acesso Condicional

As informações deste artigo podem ser usadas para resolver resultados inesperados de login relacionados com o Acesso Condicional usando mensagens de erro e registos de logins AD Azure.

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

## <a name="next-steps"></a>Passos seguintes

- [Relatórios de atividade de início de sessão no portal do Azure Active Directory](../reports-monitoring/concept-sign-ins.md)
- [Resolução de problemas Acesso Condicional usando a ferramenta E Se](troubleshoot-conditional-access-what-if.md)
- Melhores práticas para [acesso condicional no Diretório Ativo Azure](best-practices.md)
