---
title: Questões conhecidas com conformidade do protocolo SCIM 2.0 - Azure AD
description: Como resolver problemas comuns de compatibilidade do protocolo enfrentados ao adicionar uma aplicação não-galeria que suporta o SCIM 2.0 a AD Azure
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 12/03/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 441d830c7512b7d06c5d4f3e64dc59844b764453
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387171"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Questões e resoluções conhecidas com o protocolo SCIM 2.0 cumprimento do serviço de fornecimento de utilizadores Azure AD

O Azure Ative Directory (Azure AD) pode automaticamente forneça utilizadores e grupos a qualquer aplicação ou sistema que seja frontalizado por um serviço web com a interface definida no [Sistema de Gestão de Identidade de Domínio Cruzado (SCIM) 2.0 especificação do protocolo](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

O suporte da AZure AD para o protocolo SCIM 2.0 é descrito na [Utilização do Sistema de Gestão de Identidade de Domínio Cruzado (SCIM) para fornecer automaticamente utilizadores e grupos do Azure Ative Directory às aplicações](use-scim-to-provision-users-and-groups.md), que lista as partes específicas do protocolo que implementa de forma a fornecer automaticamente utilizadores e grupos de Azure AD a aplicações que suportam o SCIM 2.0.

Este artigo descreve problemas atuais e passados com a adesão do serviço de fornecimento de utilizadores Azure AD ao protocolo SCIM 2.0 e como contornar estas questões.

> [!IMPORTANT]
> A última atualização ao serviço de fornecimento de utilizadores Azure AD scim foi feita no dia 18 de dezembro de 2018. Esta atualização abordou as questões de compatibilidade conhecidas enumeradas no quadro abaixo. Consulte as perguntas mais frequentes abaixo para mais informações sobre esta atualização.

## <a name="scim-20-compliance-issues-and-status"></a>PROBLEMAS DE CONFORMIDADE SCIM 2.0 e estado

| **Emissão de conformidade SCIM 2.0** |  **Fixo?** | **Data de fixação**  |  
|---|---|---|
| A AD AZure exige que "/scim" esteja na raiz do URL do ponto final scim da aplicação  | Sim  |  18 de dezembro de 2018 | 
| Atributos de extensão usam ponto "." notação antes de atribuir nomes em vez de cólon ":" notação |  Sim  | 18 de dezembro de 2018  | 
|  Os pedidos de remendos para atributos de vários valores contêm sintaxe de filtro de caminho inválido | Sim  |  18 de dezembro de 2018  | 
|  Os pedidos de criação de grupo contêm um esquema inválido URI | Sim  |  18 de dezembro de 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>As correções de serviços foram descritas automaticamente na minha aplicação SCIM pré-existente?

Não. Como teria constituído uma alteração de rutura nas aplicações SCIM que estavam codificadas para trabalhar com o comportamento mais antigo, as alterações não eram automaticamente aplicadas às aplicações existentes.

As alterações são aplicadas a todas as novas aplicações SCIM não-galeria configuradas no portal Azure, após a data da correção.

Para obter informações sobre como migrar um trabalho de fornecimento de utilizadores pré-existente para incluir as correções mais recentes, consulte a secção seguinte.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Posso migrar um trabalho de fornecimento de utilizadores baseado no SCIM existente para incluir as correções de serviço mais recentes?

Yes. Se já estiver a utilizar esta aplicação para um único sinal de adesão e precisar de migrar o trabalho de provisão existente para incluir as correções mais recentes, siga o procedimento abaixo. Este procedimento descreve como usar a API do Microsoft Graph e o microsoft Graph API explorer para remover o seu antigo trabalho de provisionamento da sua app SCIM existente, e criar um novo que exibe o novo comportamento.

> [!NOTE]
> Se a sua aplicação ainda estiver em desenvolvimento e ainda não tiver sido implementada para um único sign-on ou provisionamento do utilizador, a solução mais fácil é eliminar a entrada de aplicação na secção **Azure Ative Directory > Aplicações empresariais** do portal Azure, e simplesmente adicionar uma nova entrada para a aplicação utilizando a **aplicação Create >** opção Não galeria. Esta é uma alternativa para executar o procedimento abaixo.
 
1. Inscreva-se no portal Azure em https://portal.azure.com .
2. Na secção **Azure Ative Directory > Aplicações Empresariais** do portal Azure, localize e selecione a aplicação SCIM existente.
3. Na secção **Propriedades** da sua aplicação SCIM existente, copie o **ID do objeto.**
4. Numa nova janela do navegador web, vá https://developer.microsoft.com/graph/graph-explorer e inscreva-se como administrador do inquilino Azure AD onde a sua aplicação é adicionada.
5. No Graph Explorer, executar o comando abaixo para localizar o ID do seu trabalho de provisionamento. Substitua "[object-id]" pelo ID principal de serviço (ID do objeto) copiado a partir do terceiro passo.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Obter empregos](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Obter empregos") 


6. Nos resultados, copie a cadeia completa de "ID" que começa com "customappsso" ou "scim".
7. Executar o comando abaixo para recuperar a configuração de mapeamento de atributos, para que possa fazer uma cópia de segurança. Use o mesmo [object-id] como antes, e substitua [job-id] pelo iD de trabalho de provisionamento copiado a partir do último passo.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Obter Schema](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Obter Schema") 

8. Copie a saída JSON do último passo e guarde-a num ficheiro de texto. Isto contém quaisquer mapeamentos de atributos personalizados que adicionou à sua antiga aplicação, e deve ser aproximadamente alguns milhares de linhas de JSON.
9. Executar o comando abaixo para eliminar o trabalho de provisionamento:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Executar o comando abaixo para criar um novo trabalho de provisionamento que tenha as mais recentes correções de serviço.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Nos resultados do último passo, copie a cadeia completa de "ID" que começa com "scim". Opcionalmente, volte a aplicar os seus antigos mapeamentos de atributos executando o comando abaixo, substituindo [id de novo emprego] pelo novo ID de trabalho que acabou de copiar, e introduzindo a saída JSON a partir do passo #7 como o órgão de pedido.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Volte à primeira janela do navegador web e selecione o **separador Provisioning** para a sua aplicação.
13. Verifique a sua configuração e, em seguida, inicie o trabalho de provisionamento. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Posso adicionar uma nova aplicação não-galeria que tenha o comportamento de provisionamento do utilizador antigo?

Yes. Se tivesse codificado uma aplicação para o comportamento antigo que existia antes das correções, e precisasse de implementar uma nova instância, siga o procedimento abaixo. Este procedimento descreve como usar a API do Microsoft Graph e o explorador de API do Microsoft Graph para criar um trabalho de provisão SCIM que exibe o comportamento antigo.
 
1. Inscreva-se no portal Azure em https://portal.azure.com .
2. no **Azure Ative Directory > Aplicações Empresariais > Criar** a secção de aplicação do portal Azure, criar uma nova aplicação **não-galeria.**
3. Na secção **Propriedades** da sua nova aplicação personalizada, copie o **ID do objeto.**
4. Numa nova janela do navegador web, vá https://developer.microsoft.com/graph/graph-explorer e inscreva-se como administrador do inquilino Azure AD onde a sua aplicação é adicionada.
5. No Graph Explorer, execute o comando abaixo para rubricar a configuração de provisionamento para a sua aplicação.
   Substitua "[object-id]" pelo ID principal de serviço (ID do objeto) copiado a partir do terceiro passo.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Volte à primeira janela do navegador web e selecione o **separador Provisioning** para a sua aplicação.
7. Preencha a configuração de provisionamento do utilizador como normalmente faria.


## <a name="next-steps"></a>Próximos passos
[Saiba mais sobre o provisionamento e desavisionamento das aplicações do SaaS](user-provisioning.md)

