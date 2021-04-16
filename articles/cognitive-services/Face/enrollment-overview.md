---
title: Melhores práticas para adicionar utilizadores a um serviço Face
titleSuffix: Azure Cognitive Services
description: Conheça o processo de inscrição face para registar utilizadores num serviço de reconhecimento facial.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: e710771dd14b389e856e752b6587e2a76ad9d85b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505076"
---
# <a name="best-practices-for-adding-users-to-a-face-service"></a>Melhores práticas para adicionar utilizadores a um serviço Face

Para utilizar a API dos Serviços Cognitivos para verificação ou identificação facial, é necessário inscrever rostos num **LargePersonGroup**. Este mergulho profundo demonstra as melhores práticas para recolher consentimento significativo dos utilizadores, bem como lógica de exemplo para criar inscrições de alta qualidade que irão otimizar a precisão do reconhecimento.  

## <a name="meaningful-consent"></a>Consentimento significativo 

Um dos principais propósitos de uma aplicação de inscrição para reconhecimento facial é dar aos utilizadores a oportunidade de consentir em usar imagens do seu rosto para fins específicos, como o acesso a um local de trabalho. Como as tecnologias de reconhecimento facial podem ser entendidas como a recolha de dados pessoais sensíveis, é especialmente importante pedir consentimento de uma forma transparente e respeitosa. O consentimento é significativo para os utilizadores quando os capacita para tomar a decisão que eles sentem que é melhor para eles.   

Com base na pesquisa de utilizadores da Microsoft, nos princípios responsáveis da Microsoft em IA e [na investigação externa,](ftp://ftp.cs.washington.edu/tr/2000/12/UW-CSE-00-12-02.pdf)descobrimos que o consentimento é significativo quando oferece o seguinte aos utilizadores que se matriculam na tecnologia:

* Consciência: Os utilizadores não devem ter dúvidas quando lhes é pedido que forneçam o seu modelo facial ou fotos de inscrição. 
* Compreensão: Os utilizadores devem ser capazes de descrever com precisão, nas suas próprias palavras, o que lhes foi pedido, por quem, para que fim e com que garantias. 
* Liberdade de escolha: Os utilizadores não devem sentir-se coagidos ou manipulados na hora de escolher se concordam e se inscrevem no reconhecimento facial. 
* Controlo: Os utilizadores devem poder revogar o seu consentimento e apagar os seus dados a qualquer momento. 

Esta secção oferece orientações para o desenvolvimento de uma aplicação de inscrição para reconhecimento facial. Esta orientação foi desenvolvida com base na pesquisa de utilizadores da Microsoft no contexto da inscrição de indivíduos em reconhecimento facial para a entrada na construção. Por conseguinte, estas recomendações podem não se aplicar a todas as soluções de reconhecimento facial. O uso responsável da API face depende fortemente do contexto específico em que está integrado, pelo que a priorização e aplicação destas recomendações devem ser adaptadas ao seu cenário. 

> [!NOTE]
> É da sua responsabilidade alinhar o seu pedido de inscrição com os requisitos legais aplicáveis na sua jurisdição e refletir com precisão todas as suas práticas de recolha e processamento de dados.

## <a name="application-development"></a>Programação de aplicações 

Antes de desenhar um fluxo de inscrição, pense em como a aplicação que está a construir pode cumprir as promessas que faz aos utilizadores sobre como os seus dados são protegidos. As seguintes recomendações podem ajudá-lo a construir uma experiência de inscrição que inclua abordagens responsáveis para garantir dados pessoais, gerir a privacidade dos utilizadores e garantir que a aplicação está acessível a todos os utilizadores.  

|Categoria | Recomendações |
|---|---|
|Hardware | Considere a qualidade da câmara do dispositivo de inscrição. |
|Características de inscrição recomendadas | Inclua um passo de login com autenticação multi-factor.</br></br>Ligue as informações do utilizador como um pseudónimo ou número de identificação com o seu ID do modelo de rosto da API facial (conhecida como ID da pessoa). Este mapeamento é necessário para recuperar e gerir a inscrição de um utilizador. Nota: a identificação da pessoa deve ser tratada como um segredo na aplicação.</br></br>Crie um processo automatizado para eliminar todos os dados de inscrição, incluindo os modelos de rosto e fotos de inscrição de pessoas que já não são utilizadores de tecnologia de reconhecimento facial, como antigos funcionários.</br></br>Evite a inscrição automática, uma vez que não dá ao utilizador a consciência, compreensão, liberdade de escolha ou controlo recomendado para obter o consentimento. </br></br>Peça permissão aos utilizadores para guardar as imagens utilizadas para a inscrição. Isto é útil quando há uma atualização de modelo, uma vez que novas fotos de inscrição serão necessárias para se inscrever no novo modelo a cada 10 meses. Se as imagens originais não forem guardadas, os utilizadores terão de passar pelo processo de inscrição desde o início.</br></br>Permitir que os utilizadores optem por não armazenar fotografias no sistema. Para tornar a escolha mais clara, pode adicionar um segundo ecrã de pedido de consentimento para guardar as fotos de inscrição. </br></br>Se as fotos forem guardadas, crie um processo automatizado para reinscreva todos os utilizadores quando houver uma atualização de modelo. Aqueles que salvaram as suas fotos de inscrição não terão que se inscrever novamente. </br></br>Crie uma funcionalidade de aplicação que permita aos administradores designados sobrepor certos filtros de qualidade se um utilizador tiver problemas em inscrever-se. |
|Segurança | Os Serviços Cognitivos seguem [as melhores práticas](../cognitive-services-virtual-networks.md?tabs=portal) para encriptar os dados dos utilizadores em repouso e em trânsito. Seguem-se práticas adicionais que podem ajudar a cumprir as promessas de segurança que faz aos utilizadores durante a experiência de inscrição. </br></br>Tome medidas de segurança para garantir que ninguém tenha acesso à identificação da pessoa em qualquer ponto durante a inscrição. Nota: O PersonID deve ser tratado como um segredo no sistema de inscrição. </br></br>Use [o controlo de acesso baseado em funções](../../role-based-access-control/overview.md) com serviços cognitivos. </br></br>Utilize autenticação baseada em símbolos e/ou assinaturas de acesso partilhado (SAS) sobre chaves e segredos para aceder a recursos como bases de dados. Ao utilizar o pedido ou fichas SAS, pode conceder acesso limitado aos dados sem comprometer as chaves da sua conta, e pode especificar um prazo de validade no token. </br></br>Nunca guarde segredos, chaves ou senhas na sua aplicação. |
|Privacidade do utilizador |Fornecer um leque de opções de inscrição para abordar diferentes níveis de preocupações de privacidade. Não maneça que as pessoas utilizem os seus dispositivos pessoais para se inscreverem num sistema de reconhecimento facial. </br></br>Permitir que os utilizadores se reinscrevam, revoguem o consentimento e apaguem os dados da aplicação de inscrição a qualquer momento e por qualquer motivo. |
|Acessibilidade |Siga as normas de acessibilidade (por exemplo, [ADA](https://www.ada.gov/regs2010/2010ADAStandards/2010ADAstandards.htm) ou [W3C)](https://www.w3.org/TR/WCAG21/)para garantir que a aplicação é utilizável por pessoas com mobilidade ou deficiências visuais. |

## <a name="next-steps"></a>Passos seguintes  

Siga o Guia [de Aplicações de Inscrição](build-enrollment-app.md) para começar com uma aplicação de inscrição de amostras. Em seguida, personalize-o ou escreva a sua própria app de acordo com as necessidades do seu produto.