---
title: Configure um Passe de Acesso Temporário em Azure AD para registar métodos de autenticação sem palavras-passe
description: Saiba como configurar e permitir que os utilizadores registem métodos de autenticação sem palavras-passe utilizando um Passe de Acesso Temporário
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8774df6a2eee15f8b5a0c37362e5b20f14b07549
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167367"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Configure o Passe de Acesso Temporário em Azure AD para registar métodos de autenticação sem palavras-passe (Pré-visualização)

Os métodos de autenticação sem palavras-passe, como o FIDO2 e o Sign-in do telefone sem palavras-passe através da aplicação Microsoft Authenticator, permitem que os utilizadores assinem com segurança sem senha. Os utilizadores podem arrancar métodos sem palavras-passe de uma de duas maneiras:

- Utilizando métodos de autenticação multi-factor Azure AD existentes 
- Utilização de um Passe de Acesso Temporário (TAP) 

Um Passe de Acesso Temporário é uma senha limitada emitida por um administrador que satisfaz os requisitos de autenticação forte e pode ser usado para embarcar em outros métodos de autenticação, incluindo os sem palavras-passe. Um Passe de Acesso Temporário também facilita a recuperação quando um utilizador perdeu ou esqueceu o seu forte fator de autenticação, como uma chave de segurança FIDO2 ou uma aplicação do Microsoft Authenticator, mas precisa de se inscrever para registar novos métodos de autenticação forte.


Este artigo mostra-lhe como ativar e utilizar um Passe de Acesso Temporário em Azure AD utilizando o portal Azure. Também pode executar estas ações utilizando as APIs REST. 

>[!NOTE]
>O Passe de Acesso Temporário encontra-se atualmente em pré-visualização pública. Algumas funcionalidades podem não ser suportadas ou têm capacidades limitadas. 

## <a name="enable-the-temporary-access-pass-policy"></a>Ativar a política do Passe de Acesso Temporário

Uma política de Passe de Acesso Temporário define configurações, como a vida útil dos passes criados no arrendatário, ou os utilizadores e grupos que podem usar um Passe de Acesso Temporário para iniciar sposição. Antes que alguém possa iniciar sação com um Passe de Acesso Temporário, tem de ativar a política do método de autenticação e escolher quais os utilizadores e grupos que podem iniciar sação através de um Passe de Acesso Temporário.
Embora possa criar um Passe de Acesso Temporário para qualquer utilizador, apenas os incluídos na apólice podem iniciar sessão com o mesmo.

Os titulares de funções de administrador de política de administrador de administradores de acesso temporário podem atualizar a política do método de autenticação do Passe de Acesso Temporário.
Para configurar a política do método de autenticação do Passe de Acesso Temporário:

1. Inscreva-se no portal Azure como administrador global e clique nos métodos de autenticação temporária de acesso temporário do **Diretório Azure**  >    >    >  Ative.
1. Clique **em Sim** para ativar a política, selecione quais os utilizadores que têm a política aplicada e quaisquer configurações **gerais.**

   ![Screenshot de como permitir a política do método de autenticação do Passe de Acesso Temporário](./media/how-to-authentication-temporary-access-pass/policy.png)

   O valor predefinido e a gama de valores permitidos são descritos no quadro seguinte.


   | Definição | Valores predefinidos | Valores permitidos | Comentários |
   |---|---|---|---|
   | Duração mínima | Uma hora | 10 - 43200 Minutos (30 dias) | Número mínimo de minutos que o Passe de Acesso Temporário é válido. |
   | Duração máxima | 24 horas | 10 - 43200 Minutos (30 dias) | Número máximo de minutos que o Passe de Acesso Temporário é válido. |
   | Tempo de vida predefinido | Uma hora | 10 - 43200 Minutos (30 dias) | Os valores predefinidos podem ser sobrepõe-se aos passes individuais, dentro do mínimo e máximo de vida configurado pela política. |
   | Uso único | Falso | Verdadeiro / Falso | Quando a apólice é definida como falsa, os passes no arrendatário podem ser usados uma ou mais uma vez durante a sua validade (duração máxima de vida). Ao impor uma utilização única na política do Passe de Acesso Temporário, todos os passes criados no arrendatário serão criados como uso único. |
   | Comprimento | 8 | 8-48 caracteres | Define o comprimento da senha. |

## <a name="create-a-temporary-access-pass"></a>Criar um passe de acesso temporário

Depois de ativar uma política, pode criar um Passe de Acesso Temporário para um utilizador em Azure AD. Estas funções podem executar as seguintes ações relacionadas com um Passe de Acesso Temporário.

- O administrador global pode criar, eliminar, ver um Passe de Acesso Temporário em qualquer utilizador (exceto eles próprios)
- Os administradores de autenticação privilegiada podem criar, excluir, ver um Passe de Acesso Temporário sobre administradores e membros (exceto eles próprios)
- Os administradores de autenticação podem criar, excluir, ver um Passe de Acesso Temporário sobre os membros (exceto eles próprios)
- O Administrador Global pode ver os detalhes do Passe de Acesso Temporário no utilizador (sem ler o código em si).

1. Inscreva-se no portal Azure como administrador global, administrador de autenticação privilegiada ou administrador de autenticação. 
1. Clique em **Azure Ative Directory,** navegue para os Utilizadores, selecione um utilizador, como *Chris Green,* e depois escolha **métodos de autenticação**.
1. Se necessário, selecione a opção para **experimentar a experiência dos novos métodos de autenticação do utilizador**.
1. Selecione a opção para **adicionar métodos de autenticação.**
1. Abaixo **Escolha o método**, clique no Passe de Acesso Temporário **(Pré-visualização)**.
1. Defina um tempo ou duração de ativação personalizado e clique em **Adicionar**.

   ![Screenshot de como criar um Passe de Acesso Temporário](./media/how-to-authentication-temporary-access-pass/create.png)

1. Uma vez adicionados, os detalhes do Passe de Acesso Temporário são mostrados. Tome nota do valor real do Passe de Acesso Temporário. Fornece este valor ao utilizador. Não pode ver este valor depois de clicar **em Ok**.
   
   ![Screenshot dos detalhes do Passe de Acesso Temporário](./media/how-to-authentication-temporary-access-pass/details.png)

Os seguintes comandos mostram como criar e obter um Passe de Acesso Temporário utilizando o PowerShell:

```powershell
# Create a Temporary Access Pass for a user
$properties = @{}
$properties.isUsableOnce = $True
$properties.startDateTime = '2021-03-11 06:00:00'
$propertiesJSON = $properties | ConvertTo-Json

New-MgUserAuthenticationTemporaryAccessPassMethod -UserId user2@contoso.com -BodyParameter $propertiesJSON

Id                                   CreatedDateTime       IsUsable IsUsableOnce LifetimeInMinutes MethodUsabilityReason StartDateTime         TemporaryAccessPass
--                                   ---------------       -------- ------------ ----------------- --------------------- -------------         -------------------
c5dbd20a-8b8f-4791-a23f-488fcbde3b38 9/03/2021 11:19:17 PM False    True         60                NotYetValid           11/03/2021 6:00:00 AM TAPRocks!

# Get a user's Temporary Access Pass
Get-MgUserAuthenticationTemporaryAccessPassMethod -UserId user3@contoso.com

Id                                   CreatedDateTime       IsUsable IsUsableOnce LifetimeInMinutes MethodUsabilityReason StartDateTime         TemporaryAccessPass
--                                   ---------------       -------- ------------ ----------------- --------------------- -------------         -------------------
c5dbd20a-8b8f-4791-a23f-488fcbde3b38 9/03/2021 11:19:17 PM False    True         60                NotYetValid           11/03/2021 6:00:00 AM

```

## <a name="use-a-temporary-access-pass"></a>Use um passe de acesso temporário

O uso mais comum para um Passe de Acesso Temporário é para um utilizador registar detalhes de autenticação durante a primeira entrada, sem a necessidade de completar pedidos de segurança adicionais. Os métodos de autenticação estão registados em [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Os utilizadores também podem atualizar os métodos de autenticação existentes aqui.

1. Abra um navegador web para [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Insira a UPN da conta para a que criou o Passe de Acesso Temporário, como *tapuser@contoso.com* .
1. Se o utilizador estiver incluído na política do Passe de Acesso Temporário, verá um ecrã para introduzir o seu Passe de Acesso Temporário.
1. Introduza o Passe de Acesso Temporário que foi apresentado no portal Azure.

   ![Screenshot de como entrar num Passe de Acesso Temporário](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>Para domínios federados, um Passe de Acesso Temporário é preferido sobre a federação. Um utilizador com um Passe de Acesso Temporário completará a autenticação em Azure AD e não será redirecionado para o Fornecedor de Identidade Federado (IdP).

O utilizador já se inscreveu e pode atualizar ou registar um método como a chave de segurança FIDO2. Os utilizadores que atualizam os seus métodos de autenticação devido à perda das suas credenciais ou dispositivo devem certificar-se de que removem os métodos antigos de autenticação.

Os utilizadores também podem utilizar o seu Passe de Acesso Temporário para se registarem para o acesso telefónico sem palavras-passe diretamente da aplicação Authenticator. Para obter mais informações, consulte [Adicionar o seu trabalho ou conta escolar à aplicação Microsoft Authenticator](../user-help/user-help-auth-app-add-work-school-account.md).

![Screenshot de como introduzir um Passe de Acesso Temporário usando trabalho ou conta escolar](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-temporary-access-pass"></a>Apagar um passe de acesso temporário

Um passe de acesso temporário expirado não pode ser usado. De acordo com os **métodos de autenticação** para um utilizador, a coluna **Details** mostra quando o Passe de Acesso Temporário expirou. Pode eliminar um Passe de Acesso Temporário expirado utilizando os seguintes passos:

1. No portal AD Azure, navegue para **os Utilizadores,** selecione um utilizador, como *o Utilizador tap,* em seguida, escolha **métodos de autenticação**.
1. No lado direito do método de autenticação do **Passe de Acesso Temporário (Pré-visualização)** indicado na lista, selecione **Delete**.

Também pode utilizar o PowerShell:

```powershell
# Remove a user's Temporary Access Pass
Remove-MgUserAuthenticationTemporaryAccessPassMethod -UserId user3@contoso.com -TemporaryAccessPassAuthenticationMethodId c5dbd20a-8b8f-4791-a23f-488fcbde3b38
```

## <a name="replace-a-temporary-access-pass"></a>Substitua um Passe de Acesso Temporário 

- Um utilizador só pode ter um Passe de Acesso Temporário. A senha pode ser utilizada durante o início e o fim do Passe de Acesso Temporário.
- Se o utilizador necessitar de um novo Passe de Acesso Temporário:
  - Se o passe de acesso temporário existente for válido, o administrador precisa de eliminar o passe de acesso temporário existente e criar um novo passe para o utilizador. A eliminação de um passe de acesso temporário válido revogará as sessões do utilizador. 
  - Se o passe de acesso temporário existente tiver expirado, um novo Passe de Acesso Temporário irá anular o passe de acesso temporário existente e não revogará as sessões do utilizador.

Para obter mais informações sobre as normas NIST para embarque e recuperação, consulte a [Publicação Especial NIST 800-63A](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Limitações

Tenha em mente estas limitações:

- Ao utilizar um Passe de Acesso Temporário único para registar um método sem palavras-passe, como FIDO2 ou acesso telefónico, o utilizador deve completar o registo no prazo de 10 minutos após a entrada com o passe de acesso temporário único. Esta limitação não se aplica a um Passe de Acesso Temporário que possa ser utilizado mais de uma vez.
- Os utilizadores convidados não podem iniciar sedução com um Passe de Acesso Temporário.
- Os utilizadores no âmbito da política de registo de autosserviço de reset (SSPR) *ou* [da política de registo de autenticação multi-factor de proteção de identidade](../identity-protection/howto-identity-protection-configure-mfa-policy.md) serão obrigados a registar métodos de autenticação depois de terem assinado com um Passe de Acesso Temporário. Os utilizadores com âmbito para estas políticas serão redirecionados para o [modo de interrupção do registo combinado](concept-registration-mfa-sspr-combined.md#combined-registration-modes). Esta experiência não suporta atualmente o registo fido2 e de inscrição de inscrição no telefone. 
- Um passe de acesso temporário não pode ser utilizado com a extensão do Servidor de Política de Rede (NPS) e o adaptador ative Directory Federation Services (AD FS) ou durante a configuração/experiência fora de caixa do Windows (OOBE) e AutoPilot. 
- Quando o SSO sem emenda está ativado no arrendatário, os utilizadores são solicitados a introduzir uma senha. Em **vez disso,** o link Use o seu Passe de Acesso Temporário estará disponível para o utilizador iniciar sação com um Passe de Acesso Temporário.

  ![Screenshot de usar um passe de acesso temporário em vez](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Resolução de problemas    

- Se um Passe de Acesso Temporário não for oferecido a um utilizador durante a sindução, verifique o seguinte:
  - O utilizador está no âmbito da política de autenticação do Passe de Acesso Temporário.
  - O utilizador tem um Passe de Acesso Temporário válido, e se for uma única utilização, ainda não foi utilizado.
- Se o **sinal do Passe de Acesso Temporário foi bloqueado devido à Política de Credencial do Utilizador** aparecer durante a entrada com um Passe de Acesso Temporário, verifique o seguinte:
  - O utilizador tem um Passe de Acesso Temporário multiusos enquanto a política do método de autenticação requer um passe de acesso temporário único.
  - Já foi usado um passe de acesso temporário único.

## <a name="next-steps"></a>Passos seguintes

- [Planeie uma implementação de autenticação sem palavras-passe no Azure Ative Directory](howto-authentication-passwordless-deployment.md)

