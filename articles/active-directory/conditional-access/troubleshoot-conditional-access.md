---
title: Problemas de sessão de resolução de problemas com acesso condicional - Diretório Ativo Azure
description: Este artigo descreve o que fazer quando as suas políticas de Acesso Condicional resultam em resultados inesperados
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 03/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf1daca79f3ed20d9b7a89af20d74ff5f3148b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337444"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Problemas de suturação de problemas com acesso condicional

As informações neste artigo podem ser usadas para resolver resultados inesperados de entrada relacionados com o Acesso Condicional utilizando mensagens de erro e registo de login de Anúncios Azure.

## <a name="conditional-access-sign-in-interrupt"></a>Interrupção de acesso condicional

A primeira forma é rever a mensagem de erro que aparece. Para problemas de sessão ao utilizar um navegador web, a própria página de erro tem informações detalhadas. Só esta informação pode descrever qual é o problema e isso pode sugerir uma solução.

![Iniciar sessão por engano - dispositivo conforme necessário](./media/troubleshoot-conditional-access/image1.png)

No erro acima referido, a mensagem indica que a aplicação só pode ser acedida a partir de dispositivos ou aplicações de clientes que cumpram a política de gestão de dispositivos móveis da empresa. Neste caso, a aplicação e o dispositivo não cumprem essa política.

## <a name="azure-ad-sign-in-events"></a>Eventos de inscrição da AD Azure

O segundo método para obter informações detalhadas sobre a interrupção do sign-in é rever os eventos de entrada da AD Azure para ver que políticas ou políticas de acesso condicional foram aplicadas e porquê.

Mais informações podem ser encontradas sobre o problema clicando em **Mais Detalhes** na página de erro inicial. Clicar em **Mais Detalhes** revelará informações de resolução de problemas que são úteis ao pesquisar os eventos de entrada de AD Azure para o evento de falha específico que o utilizador viu ou ao abrir um incidente de suporte com a Microsoft.

![Mais detalhes de um Acesso Condicional interrompido sessão de navegador web.](./media/troubleshoot-conditional-access/image2.png)

Para saber qual a política ou políticas de acesso condicional aplicadas e por que fazer o seguinte.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou leitor global.
1. Navegue nos > **Sign-ins** **de Diretório Ativo azure**.
1. Encontre o evento para o início de sessão para rever. Adicione ou remova filtros e colunas para filtrar informações desnecessárias.
   1. Adicione filtros para estreitar o âmbito:
      1. **Identificação** da correlação quando se tem um evento específico para investigar.
      1. **Acesso condicional** para ver falha política e sucesso. Procure o seu filtro para mostrar apenas falhas na limitação dos resultados.
      1. **Nome de utilizador** para ver informações relacionadas com utilizadores específicos.
      1. **Data** marcada para o prazo em questão.

   ![Selecionando o filtro de acesso condicional no registo de sessão](./media/troubleshoot-conditional-access/image3.png)

1. Uma vez encontrado o registo de sessão que corresponda à falha de inscrição do utilizador, selecione o separador **Acesso Condicional.** O separador Acesso Condicional mostrará a política ou políticas específicas que resultaram na interrupção do registo.
   1. As informações no separador de resolução de **problemas e suporte** podem fornecer uma razão clara para que um inserido falhou, como um dispositivo que não preencheu os requisitos de conformidade.
   1. Para investigar mais, descumes na configuração das políticas clicando no Nome da **Política**. Clicar no Nome de **Política** mostrará a interface de utilizador de configuração de políticas para a política selecionada para revisão e edição.
   1. Os dados do **utilizador** cliente e do **dispositivo** que foram utilizados para a avaliação da política de Acesso Condicional também estão disponíveis na **Informação Básica**, **Localização,** Informações do **Dispositivo,** Detalhes de **Autenticação**e **Detalhes Adicionais** do evento de início de sessão.

   ![Assine em caso de acesso condicional](./media/troubleshoot-conditional-access/image5.png)

Se a informação no evento não for suficiente para compreender os resultados do início de sessão ou ajustar a política para obter os resultados desejados, então poderá ser aberto um incidente de apoio. Navegue para o separador de resolução de **problemas e suporte** do evento de início de evento e selecione Criar um novo pedido de **suporte**.

![O separador de resolução de problemas e suporte do evento Sign-in](./media/troubleshoot-conditional-access/image6.png)

Ao submeter o incidente, forneça o ID de pedido e a hora e data do evento de início de sessão nos detalhes de submissão do incidente. Estas informações permitirão ao suporte da Microsoft encontrar o evento com o qual está preocupado.

### <a name="conditional-access-error-codes"></a>Códigos de erro de acesso condicional

| Código de Erro de Inscrição | Cadeia de erro |
| --- | --- |
| 53000 | DispositivoNotcompliant |
| 53001 | DispositivoNotDomainJoined |
| 53002 | AplicaçõesUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>Passos seguintes

- [Relatórios de atividade de início de sessão no portal do Azure Active Directory](../reports-monitoring/concept-sign-ins.md)
- [Resolução de problemas Acesso Condicional utilizando a ferramenta What If](troubleshoot-conditional-access-what-if.md)
- Boas práticas para [acesso condicional em Diretório Ativo Azure](best-practices.md)
