---
title: Método de autenticação de perguntas de segurança - Azure Ative Directory
description: Saiba como usar questões de segurança no Azure Ative Directory para ajudar a melhorar e garantir eventos de acesso
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bf90909cdd3bd3013dc43d50a9589945f5215c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532736"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Métodos de autenticação no Diretório Ativo Azure - questões de segurança

As questões de segurança não são usadas como um método de autenticação durante um evento de inscrição. Em vez disso, as questões de segurança podem ser usadas durante o processo de reset da palavra-passe de autosserviço (SSPR) para confirmar quem é. As contas do administrador não podem usar questões de segurança como método de verificação com sSPR.

Quando os utilizadores se registam na SSPR, são solicitados a escolher os métodos de autenticação a utilizar. Se optarem por usar questões de segurança, escolhem de um conjunto de perguntas para solicitar e, em seguida, fornecer as suas próprias respostas.

![Screenshot do portal Azure que mostra métodos de autenticação e opções para questões de segurança](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> As questões de segurança são armazenadas de forma privada e segura num objeto de utilizador no diretório e só podem ser respondidas pelos utilizadores durante o registo. Não há como um administrador ler ou modificar as perguntas ou respostas de um utilizador.

As questões de segurança podem ser menos seguras do que outros métodos, porque algumas pessoas podem saber as respostas às perguntas de outro utilizador. Se utilizar questões de segurança com sSPR, é aconselhável usá-las em conjunto com outro método. Um utilizador pode ser solicitado a utilizar a App autenticadora da Microsoft ou a autenticação do telefone para verificar a sua identidade durante o processo SSPR, e escolher questões de segurança apenas se não tiver o seu telemóvel ou dispositivo registado com eles.

## <a name="predefined-questions"></a>Perguntas predefinidas

As seguintes questões de segurança predefinidas estão disponíveis para utilização como método de verificação com a SSPR. Todas estas questões de segurança são traduzidas e localizadas no conjunto completo de idiomas microsoft 365 baseados no local do navegador do utilizador:

* Em que cidade conheceu o seu primeiro cônjuge/parceiro?
* Em que cidade os seus pais se conheceram?
* Em que cidade vive o seu irmão mais próximo?
* Em que cidade nasceu o seu pai?
* Em que cidade foi o seu primeiro emprego?
* Em que cidade nasceu a sua mãe?
* Em que cidade estava no Ano Novo de 2000?
* Qual é o último nome do teu professor favorito no liceu?
* Qual é o nome de uma faculdade a que se candidatou, mas não frequentou?
* Qual é o nome do lugar onde fez a sua primeira receção de casamento?
* Qual é o nome do meio do seu pai?
* Qual é a sua comida favorita?
* Qual é o primeiro e último nome da sua avó materna?
* Qual é o nome do meio da sua mãe?
* Qual é o aniversário do seu irmão mais velho mês e ano? (por exemplo, novembro de 1985)
* Qual é o nome do meio do seu irmão mais velho?
* Qual é o primeiro e último nome do seu avô paterno?
* Qual é o nome do meio do seu irmão mais novo?
* Que escola frequentaste no 6º ano?
* Qual foi o primeiro e último nome do seu melhor amigo de infância?
* Qual foi o primeiro e último nome do seu primeiro outro significativo?
* Qual era o último nome da tua professora favorita da escola?
* Qual foi a ideia e modelo do seu primeiro carro ou moto?
* Qual era o nome da primeira escola que frequentaste?
* Qual era o nome do hospital em que nasceu?
* Qual era o nome da rua da sua primeira casa de infância?
* Qual era o nome do seu herói de infância?
* Qual era o nome do seu animal de peluche favorito?
* Qual era o nome do seu primeiro animal de estimação?
* Qual era o seu apelido de infância?
* Qual era o teu desporto favorito no liceu?
* Qual foi o seu primeiro emprego?
* Quais foram os últimos quatro dígitos do seu número de telefone de infância?
* Quando era jovem, o que queria ser quando crescesse?
* Quem é a pessoa mais famosa que já conheceu?

## <a name="custom-security-questions"></a>Questões de segurança personalizadas

Para uma flexibilidade adicional, pode definir as suas próprias questões de segurança personalizadas. O comprimento máximo de uma questão de segurança personalizada é de 200 caracteres.

As questões de segurança personalizadas não são automaticamente localizadas, como com as questões de segurança predefinidos. Todas as questões personalizadas são apresentadas no mesmo idioma que são inseridas na interface administrativa do utilizador, mesmo que o local do navegador do utilizador seja diferente. Se precisar de perguntas localizadas, deve utilizar as perguntas predefinidas.

## <a name="security-question-requirements"></a>Requisitos de questões de segurança

Para questões de segurança padrão e personalizadas, aplicam-se os seguintes requisitos e limitações:

* O limite mínimo de caracteres de resposta é de três caracteres.
* O limite máximo de caracteres de resposta é de 40 caracteres.
* Os utilizadores não conseguem responder à mesma pergunta mais do que uma vez.
* Os utilizadores não podem dar a mesma resposta a mais do que uma pergunta.
* Qualquer conjunto de caracteres pode ser usado para definir as perguntas e as respostas, incluindo caracteres Unicode.
* O número de perguntas definidas deve ser superior ou igual ao número de perguntas que foram obrigadas a registar-se.

## <a name="next-steps"></a>Passos seguintes

Para começar, consulte o tutorial para reiniciar a [palavra-passe de autosserviço (SSPR)][tutorial-sspr].

Para saber mais sobre os conceitos SSPR, consulte [como funciona o reset da palavra-passe de autosserviço AZure AD][concept-sspr].

Saiba mais sobre a configuração dos métodos de autenticação utilizando a [beta API do Microsoft Graph REST](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
