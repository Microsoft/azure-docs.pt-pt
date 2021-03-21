---
title: Segurança MFA baseada em telefone em Azure AD B2C
titleSuffix: Azure AD B2C
description: Aprenda dicas para garantir a autenticação de vários fatores (MFA) no seu inquilino Azure AD B2C utilizando relatórios e alertas do Azure Monitor Log Analytics. Utilize o nosso livro para identificar autenticações de telefone fraudulentas e mitigar inscrições fraudulentas. =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cc9e0be90c138ba33e1b4dfe11ea6f9c8b7da297
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033559"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>Assegurar a autenticação de vários fatores por telefone (MFA)

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Com o Azure Ative Directory (Azure AD) Multi-Factor Authentication (MFA), os utilizadores podem optar por receber uma chamada de voz automatizada num número de telefone que se inscrevam para verificação. Os utilizadores maliciosos poderiam tirar partido deste método criando várias contas e fazendo chamadas telefónicas sem completar o processo de registo do MFA. Estas numerosas inscrições falhadas poderiam esgotar as tentativas de inscrição permitidas, impedindo outros utilizadores de se inscreverem para novas contas no seu inquilino Azure AD B2C. Para ajudar a proteger contra estes ataques, pode utilizar o Azure Monitor para monitorizar falhas de autenticação de telefone e mitigar inscrições fraudulentas.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, crie um espaço de [trabalho Log Analytics](azure-monitor.md).

## <a name="create-a-phone-based-mfa-events-workbook"></a>Criar um livro de eventos MFA baseado em telefone

O [repositório Azure AD B2C Reports & Alerts](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) no GitHub contém artefactos que pode usar para criar e publicar relatórios, alertas e dashboards baseados em registos Azure AD B2C. O rascunho do livro ilustrado abaixo destaca falhas relacionadas com o telefone.

### <a name="overview-tab"></a>Separador de visão geral

As seguintes informações são mostradas no **separador Visão Geral:**

- Razões de falha (o número total de autenticações de telefones falhados por cada motivo)
- Bloqueado devido à má reputação
- Endereço IP com Autenticações telefónicas Falhadas (a contagem total de autenticações de telefone falhadas para cada endereço IP dado)
- Números de telefone com endereço IP - Autenticações de telefone falhadas
- Browser (falhas de autenticação de telefone por navegador de cliente)
- Sistema operativo (falhas de autenticação de telefone por sistema operativo do cliente)

![Separador de visão geral](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>Separador de detalhes

As seguintes informações são comunicadas no separador **Detalhes:**

- Política Azure AD B2C - Autenticações de telefone falhadas
- Falhas de autenticação do telefone por número de telefone – Gráfico de tempo (linha do tempo ajustável)
- Falhas de autenticação do telefone por Azure AD B2C Policy – Time Chart (linha temporal ajustável)
- Falhas de autenticação do telefone por endereço IP – Gráfico de tempo (linha do tempo ajustável)
- Selecione o número de telefone para ver detalhes de falha (selecione um número de telefone para uma lista detalhada de falhas)

![Separador de detalhes 1 de 3](media/phone-based-mfa/details-tab-1.png)

![Separador de detalhes 2 de 3](media/phone-based-mfa/details-tab-2.png)

![Separador de detalhes 3 de 3](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>Use o livro para identificar inscrições fraudulentas

Pode utilizar o livro para compreender eventos MFA baseados em telefone e identificar o uso potencialmente malicioso do serviço de telefonia.

1. Compreenda o que é normal para o seu inquilino respondendo a estas perguntas:

   - De onde estão as regiões de onde se espera mFA por telefone?
   - Examinar as razões apresentadas para tentativas falhadas de MFA baseadas em telefone; são considerados normais ou esperados?

2. Reconhecer as características da inscrição fraudulenta:

   - **Baseado na localização**: Examine falhas de **autenticação de telefone por endereço IP** para quaisquer contas que estejam associadas a localizações das quais não espera que os utilizadores se inscrevam.

   > [!NOTE]
   > O Endereço IP fornecido é uma região aproximada.

   - **Baseado em velocidade**: Veja as horas extraordinárias de autenticação de **telefone falhada (por dia)**, que indicam números de telefone que estão a fazer um número anormal de tentativas de autenticação de telefone falhadas por dia, ordenadas da mais alta (esquerda) para a mais baixa (direita).

3. Atenuar as inscrições fraudulentas seguindo os passos na secção seguinte.
 

## <a name="mitigate-fraudulent-sign-ups"></a>Mitigar inscrições fraudulentas

Tome as seguintes ações para ajudar a mitigar inscrições fraudulentas.

- Utilize as versões **recomendadas** dos fluxos do utilizador para fazer o seguinte:
     
   - [Ativar a função de código de acesso de uma vez por e-mail (OTP)](phone-authentication-user-flows.md) para MFA (aplica-se tanto aos fluxos de inscrição como de entrada).
   - [Configure uma política de acesso condicional](conditional-access-user-flow.md) para bloquear as inscrições com base na localização (aplica-se apenas aos fluxos de entrada e não aos fluxos de inscrição).
   - Utilize conectores API para [integrar-se com uma solução anti-bot como o reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) (aplica-se aos fluxos de inscrição).

- Remova os códigos de país que não são relevantes para a sua organização a partir do menu suspenso onde o utilizador verifica o seu número de telefone (esta alteração será aplicável a futuras inscrições):
    
   1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador global do inquilino do Azure AD B2C.

   2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.

   3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.

   4. Selecione o fluxo do utilizador e, em seguida, **selecione Idiomas**. Selecione o idioma para a localização geográfica da sua organização para abrir o painel de detalhes do idioma. (Para este exemplo, vamos selecionar **inglês para** os Estados Unidos). Selecione **a página de autenticação multifactor** e, em seguida, selecione **Descarregamento de predefinições (pt)**.
 
      ![Faça upload de novos overrides para descarregar predefinições](media/phone-based-mfa/download-defaults.png)

   5. Abra o ficheiro JSON que foi descarregado no passo anterior. No ficheiro, procure `DEFAULT` e substitua a linha por `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"` . Certifique-se de definir `Overrides` para `true` .

   > [!NOTE]
   > Pode personalizar a lista de códigos de país permitidos no `countryList` elemento (ver exemplo da [página de autenticação do fator Telefone).](localization-string-ids.md#phone-factor-authentication-page-example)

   7. Guarde o ficheiro JSON. No painel de detalhes do idioma, no **upload de novos sobreposições,** selecione o ficheiro JSON modificado para o carregar.

   8. Feche o painel e selecione **Executar o fluxo do utilizador**. Para este exemplo, confirme que **os Estados Unidos** são o único código de país disponível no dropdown:
 
      ![Queda do código do país](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [proteção de identidade e acesso condicional para Azure AD B2C](conditional-access-identity-protection-overview.md) 

- Aplicar [acesso condicional aos fluxos de utilizadores no Azure Ative Directory B2C](conditional-access-user-flow.md)