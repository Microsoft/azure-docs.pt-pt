---
title: Configure um Passe de Acesso Temporário em Azure AD para registar métodos de autenticação sem palavras-passe
description: Saiba como configurar e permitir que os utilizadores registem métodos de autenticação sem palavras-passe utilizando um Passe de Acesso Temporário (TAP)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/19/2021
ms.author: justinha
author: inbarckms
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0f49f39e6bc291c3242fe739866a015ac154a8b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651166"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Configure o Passe de Acesso Temporário em Azure AD para registar métodos de autenticação sem palavras-passe (Pré-visualização)

Os métodos de autenticação sem palavras-passe, como o FIDO2 e o Sign-in do telefone sem palavras-passe através da aplicação Microsoft Authenticator, permitem que os utilizadores assinem com segurança sem senha. Os utilizadores podem arrancar métodos sem palavras-passe de uma de duas maneiras:

- Utilizando métodos de autenticação multi-factor Azure AD existentes 
- Utilização de um Passe de Acesso Temporário (TAP) 

A TAP é uma senha limitada emitida por um administrador que satisfaz os requisitos de autenticação forte e pode ser usada para embarcar outros métodos de autenticação, incluindo os sem palavras-passe. A TAP também facilita a recuperação quando um utilizador perdeu ou esqueceu o seu forte fator de autenticação, como uma chave de segurança FIDO2 ou uma aplicação microsoft Authenticator, mas precisa de se inscrever para registar novos métodos de autenticação forte.


Este artigo mostra-lhe como ativar e utilizar um TAP em AD Azure utilizando o portal Azure. Também pode executar estas ações utilizando as APIs REST. 

>[!NOTE]
>O Passe de Acesso Temporário encontra-se atualmente em pré-visualização pública. Algumas funcionalidades podem não ser suportadas ou têm capacidades limitadas. 

## <a name="enable-the-tap-policy"></a>Ativar a política da TAP

Uma política da TAP define configurações, como a vida útil dos passes criados no arrendatário, ou os utilizadores e grupos que podem utilizar uma TAP para iniciar sposição. Antes que alguém possa fazer sôs com uma TAP, tem de ativar a política do método de autenticação e escolher quais os utilizadores e grupos que podem iniciar sação através de uma TAP.
Embora possa criar um TAP para qualquer utilizador, apenas os incluídos na apólice podem entrar com o mesmo.

Os titulares de funções de administrador de política de administrador global e método de autenticação podem atualizar a política do método de autenticação TAP.
Para configurar a política do método de autenticação TAP:

1. Inscreva-se no portal Azure como administrador global e clique nos métodos de autenticação temporária de acesso temporário do **Diretório Azure**  >    >    >  Ative.
1. Clique **em Sim** para ativar a política, selecione quais os utilizadores que têm a política aplicada e quaisquer configurações **gerais.**

   ![Screenshot de como permitir a política do método de autenticação TAP](./media/how-to-authentication-temporary-access-pass/policy.png)

   O valor predefinido e a gama de valores permitidos são descritos no quadro seguinte.


   | Definição          | Valores predefinidos | Valores permitidos               | Comentários                                                                                                                                                                                                                                                                 |   |
   |------------------|----------------|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    Duração mínima | Uma hora         | 10 - 43200 Minutos (30 dias) | Número mínimo de minutos que a TAP é válida.                                                                                                                                                                                                                         |   |
   | Duração máxima | 24 horas       | 10 - 43200 Minutos (30 dias) | Número máximo de minutos que a TAP é válido.                                                                                                                                                                                                                         |   |
   | Tempo de vida predefinido | Uma hora         | 10 - 43200 Minutos (30 dias) | Os valores predefinidos podem ser ultrapassados pelos passes individuais, dentro do mínimo e máximo de vida configurado pela política                                                                                                                                                |   |
   | Uso único     | Falso          | Verdadeiro / Falso                 | Quando a apólice é definida como falsa, os passes no arrendatário podem ser usados uma ou mais uma vez durante a sua validade (duração máxima de vida). Ao impor uma utilização única na política da TAP, todos os passes criados no arrendatário serão criados como uso único. |   |
   | Comprimento           | 8              | 8-48 caracteres              | Define o comprimento da senha.                                                                                                                                                                                                                                      |   |

## <a name="create-a-tap-in-the-azure-ad-portal"></a>Criar uma TAP no Portal AD Azure

Depois de ativar uma política TAP, pode criar uma TAP para um utilizador em Azure AD. Estas funções podem desempenhar as seguintes ações relacionadas com a TAP.

- O administrador global pode criar, eliminar, ver TAP em qualquer utilizador (exceto em si)
- Os administradores de autenticação privilegiada podem criar, eliminar, ver TAP em administradores e membros (exceto eles próprios)
- Os administradores de autenticação podem criar, eliminar, ver TAP sobre os membros (exceto eles próprios)
- O Administrador Global pode ver os detalhes da TAP no utilizador (sem ler o próprio código).

Para criar uma TAP:

1. Inscreva-se no portal como administrador global, administrador de autenticação privilegiada ou administrador de autenticação. 
1. Clique em **Azure Ative Directory,** navegue para os Utilizadores, selecione um utilizador, como *Chris Green,* e depois escolha **métodos de autenticação**.
1. Se necessário, selecione a opção para **experimentar a experiência dos novos métodos de autenticação do utilizador**.
1. Selecione a opção para **adicionar métodos de autenticação.**
1. Abaixo **Escolha o método**, clique no Passe de Acesso Temporário **(Pré-visualização)**.
1. Defina um tempo ou duração de ativação personalizado e clique em **Adicionar**.

   >[!NOTE]
   >A utilização única da TAP não é aplicada quando o Keep Me Signed In (KMSI) está ativado no arrendatário. Se estiver a criar uma TAP única, certifique-se de desativar o KMSI.

   ![Screenshot de como criar uma TAP](./media/how-to-authentication-temporary-access-pass/create.png)

1. Uma vez adicionados, os detalhes da TAP são mostrados. Tome nota do valor real da TAP. Fornece este valor ao utilizador. Não pode ver este valor depois de clicar **em Ok**.
   
   ![Screenshot dos detalhes da TAP](./media/how-to-authentication-temporary-access-pass/details.png)

## <a name="use-a-tap"></a>Utilize uma TAP

A utilização mais comum para uma TAP é para um utilizador registar detalhes de autenticação durante a primeira entrada, sem necessidade de completar pedidos de segurança adicionais. Os métodos de autenticação estão registados em [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Os utilizadores também podem atualizar os métodos de autenticação existentes aqui.

1. Abra um navegador web para [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Insira a UPN da conta para a que criou a TAP, como *tapuser@contoso.com* .
1. Se o utilizador estiver incluído na política da TAP, verá um ecrã para introduzir a sua TAP.
1. Insira a TAP que foi exibida no portal Azure.

   ![Screenshot de como entrar numa TAP](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>Para os domínios federados, a TAP é preferível à federação. Um utilizador com uma TAP completará a autenticação em Azure AD e não será redirecionado para o Fornecedor de Identidade Federado (IdP).

O utilizador já se inscreveu e pode atualizar ou registar um método como a chave de segurança FIDO2. Os utilizadores que atualizam os seus métodos de autenticação devido à perda das suas credenciais ou dispositivo devem certificar-se de que removem os métodos antigos de autenticação.

Os utilizadores também podem utilizar a sua TAP para se registarem para o registo de telefone sem palavras-passe diretamente a partir da aplicação Authenticator. Para obter mais informações, consulte [Adicionar o seu trabalho ou conta escolar à aplicação Microsoft Authenticator](../user-help/user-help-auth-app-add-work-school-account.md).

![Screenshot de como entrar numa TAP usando conta de trabalho ou escola](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-tap"></a>Excluir uma TAP

Não pode ser utilizada uma TAP caducada. De acordo com os **métodos de autenticação** para um utilizador, a coluna **Pormenor** mostra quando a TAP expirou. Pode eliminar estes TAPs expirados utilizando os seguintes passos:

1. No portal AD Azure, navegue para **os Utilizadores,** selecione um utilizador, como *o Utilizador tap,* em seguida, escolha **métodos de autenticação**.
1. No lado direito do método de autenticação do **Passe de Acesso Temporário (Pré-visualização)** indicado na lista, selecione **Delete**.

## <a name="replace-a-tap"></a>Substitua uma TAP 

- Um utilizador só pode ter uma TAP. A senha pode ser utilizada durante o início e o fim da TAP.
- Se o utilizador necessitar de uma nova TAP:
  - Se a TAP existente for válida, o administrador precisa de eliminar a TAP existente e criar um novo passe para o utilizador. A eliminação de uma TAP válida revogará as sessões do utilizador. 
  - Se a tap existente tiver expirado, uma nova TAP irá anular a tap existente e não revogará as sessões de utilização.

Para obter mais informações sobre as normas NIST para embarque e recuperação, consulte a [Publicação Especial NIST 800-63A](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Limitações

Tenha em mente estas limitações:

- Ao utilizar uma TAP única para registar um método sem palavras-passe, como o FIDO2 ou o Phone's, o utilizador deve completar o registo no prazo de 10 minutos após a entrada com a única TAP. Esta limitação não se aplica a uma TAP que possa ser utilizada mais de uma vez.
- Os utilizadores convidados não podem fazer sômis com uma TAP.
- Os utilizadores no âmbito da política de registo de Autosserviço De Reset (SSPR) serão obrigados a registar um dos métodos SSPR depois de terem assinado com a TAP. Se o utilizador apenas utilizar a chave FIDO2, exclua-a da política SSPR ou desative a política de registo SSPR. 
- A TAP não pode ser utilizada com a extensão do Servidor de Política de Rede (NPS) e o adaptador Ative Directory Federation Services (AD FS).
- A utilização única da TAP não é aplicada quando a KMSI está ativada no arrendatário.
- Quando o SSO sem emenda está ativado no arrendatário, os utilizadores são solicitados a introduzir uma senha. Em **vez disso,** o link Utilizar o seu Passe de Acesso Temporário estará disponível para o utilizador iniciar sação com a TAP.

![Screenshot de Usar uma TAP em vez](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Resolução de problemas    

- Se a TAP não for oferecida a um utilizador durante a sinse-in, verifique o seguinte:
  - O utilizador está em mira para a política de método de autenticação TAP.
  - O utilizador tem uma TAP válida, e se for uma única utilização, ainda não foi utilizada.
- Se o **sinal do Passe de Acesso Temporário foi bloqueado devido à Política de Credencial do Utilizador** aparecer durante a entrada com a TAP, verifique o seguinte:
  - O utilizador tem uma TAP multiusos enquanto a política de método de autenticação requer uma TAP única.
  - Já foi utilizada uma TAP única.

## <a name="next-steps"></a>Passos seguintes

- [Planeie uma implementação de autenticação sem palavras-passe no Azure Ative Directory](howto-authentication-passwordless-deployment.md)

