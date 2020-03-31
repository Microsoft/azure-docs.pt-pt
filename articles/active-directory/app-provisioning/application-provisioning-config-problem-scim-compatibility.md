---
title: Questões conhecidas com conformidade do protocolo SCIM 2.0 - Azure AD
description: Como resolver problemas comuns de compatibilidade do protocolo enfrentados ao adicionar uma aplicação não-galeria que suporta o SCIM 2.0 à Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dff0d4f8f0062c00351b60174c63d9c19bdfa15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522939"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Questões e resoluções conhecidas com o protocolo SCIM 2.0 conformidade do serviço de fornecimento de utilizadores da AD Azure

O Azure Ative Directory (Azure AD) pode fornecer automaticamente utilizadores e grupos a qualquer aplicação ou sistema que seja encabeçado por um serviço web com a interface definida no Sistema de Gestão de [Identidade de Domínio Scim (SCIM) 2.0 especificação do protocolo.](https://tools.ietf.org/html/draft-ietf-scim-api-19) 

O suporte da Azure AD ao protocolo SCIM 2.0 é descrito no Sistema de Utilização de Gestão de Identidade de [Domínio Transversal (SCIM) para fornecer automaticamente utilizadores e grupos do Diretório Ativo azure às aplicações,](use-scim-to-provision-users-and-groups.md)que lista as partes específicas do protocolo que implementa de forma a fornecer automaticamente utilizadores e grupos da Azure AD a aplicações que suportam o SCIM 2.0.

Este artigo descreve problemas atuais e passados com a adesão do serviço de fornecimento de utilizadores da AD Azure ao protocolo SCIM 2.0 e como contornar estas questões.

> [!IMPORTANT]
> A mais recente atualização do serviço de fornecimento de utilizadores da Azure AD foi feita no dia 18 de dezembro de 2018. Esta atualização abordou as questões de compatibilidade conhecidas enumeradas na tabela abaixo. Consulte as perguntas frequentes abaixo para obter mais informações sobre esta atualização.

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0 questões de conformidade e estatuto

| **SCIM 2.0 problema de conformidade** |  **Arranjado?** | **Fixar data**  |  
|---|---|---|
| A AD Azure exige que "/scim" esteja na raiz do URL final do ponto final da aplicação  | Sim  |  18 de dezembro de 2018 | 
| Os atributos de extensão usam ponto "." notação antes de atribuir nomes em vez de cólon ":" notação |  Sim  | 18 de dezembro de 2018  | 
|  Pedidos de patch para atributos de vários valores contêm sintaxe de filtro de caminho inválido | Sim  |  18 de dezembro de 2018  | 
|  Pedidos de criação de grupo contêm um esquema inválido URI | Sim  |  18 de dezembro de 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>As correções de serviços foram descritas automaticamente aplicadas à minha aplicação SCIM pré-existente?

Não. Como teria constituído uma alteração de rutura nas aplicações SCIM que foram codificadas para trabalhar com o comportamento mais antigo, as alterações não foram automaticamente aplicadas às aplicações existentes.

As alterações são aplicadas a todas as novas [aplicações SCIM não-galeria](../manage-apps/configure-single-sign-on-non-gallery-applications.md) configuradas no portal Azure, após a data da correção.

Para obter informações sobre como migrar um trabalho de fornecimento de utilizadores pré-existente para incluir as últimas correções, consulte a secção seguinte.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Posso migrar um trabalho de fornecimento de utilizadores baseado no SCIM existente para incluir as mais recentes correções de serviço?

Sim. Se já está a utilizar esta instância de candidatura para uma única inscrição e precisa de migrar o emprego de provisionamento existente para incluir as últimas correções, siga o procedimento abaixo. Este procedimento descreve como usar o Microsoft Graph API e o explorador da Microsoft Graph API para remover o seu antigo trabalho de provisionamento da sua aplicação SCIM existente, e criar um novo que exibe o novo comportamento.

> [!NOTE]
> Se a sua aplicação ainda estiver em desenvolvimento e ainda não tiver sido implementada para um único fornecimento de inscrição ou utilizador, a solução mais fácil é eliminar a entrada na secção de **Aplicações** empresariais > do Portal Azure, e simplesmente adicionar uma nova entrada para a aplicação utilizando a **aplicação Create >** opção Não galeria. Esta é uma alternativa para executar o procedimento abaixo.
 
1. Assine no portal Azure em https://portal.azure.com.
2. Na secção **de Aplicações empresariais > diretório si ativo do Azure,** localize e selecione a sua aplicação SCIM existente.
3. Na secção **Propriedades** da sua aplicação SCIM existente, copie o ID do **Objeto**.
4. Numa nova janela do navegador https://developer.microsoft.com/graph/graph-explorer web, vá e inscreva-se como administrador do inquilino Azure AD onde a sua aplicação é adicionada.
5. No Graph Explorer, execute o comando abaixo para localizar a identificação do seu trabalho de provisionamento. Substitua "[object-id]" pelo ID do diretor de serviço (ID do objeto) copiado a partir do terceiro passo.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Obter Empregos](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Obter Empregos") 


6. Nos resultados, copie a cadeia completa "ID" que começa com "customappsso" ou "scim".
7. Faça o comando abaixo para recuperar a configuração de mapeamento de atributos, para que possa fazer uma cópia de segurança. Use o mesmo [object-id] como antes, e substitua [o id de trabalho] pelo ID de trabalho de provisionamento copiado do último passo.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Pegue Schema](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Pegue Schema") 

8. Copie a saída JSON a partir do último passo e guarde-a para um ficheiro de texto. Isto contém quaisquer mapeamentos de atributos personalizados que você adicionou à sua antiga app, e deve ser aproximadamente alguns milhares de linhas de JSON.
9. Executar o comando abaixo para eliminar o trabalho de provisionamento:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Executar o comando abaixo para criar um novo trabalho de provisionamento que tenha as mais recentes correções de serviço.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Nos resultados do último passo, copie a cadeia completa "ID" que começa com "scim". Opcionalmente, reaplique os seus antigos mapeamentos de atributos executando o comando abaixo, substituindo [o novo trabalho-id] pelo novo ID de trabalho que acabou de copiar, e entrando na saída JSON a partir do passo #7 como o organismo de pedido.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Volte à primeira janela do navegador web e selecione o separador **Provisioning** para a sua aplicação.
13. Verifique a sua configuração e, em seguida, inicie o trabalho de provisionamento. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Posso adicionar uma nova aplicação não-galeria que tenha o comportamento de fornecimento de utilizadores antigo?

Sim. Se tiver codificado uma aplicação para o comportamento antigo que existia antes das correções, e precisando implementar uma nova instância, siga o procedimento abaixo. Este procedimento descreve como usar o Microsoft Graph API e o explorador da Microsoft Graph API para criar um trabalho de provisionamento SCIM que exibe o comportamento antigo.
 
1. Assine no portal Azure em https://portal.azure.com.
2. no **Diretório Ativo do Azure > Aplicações empresariais > Criar** a secção de aplicação do portal Azure, criar uma nova aplicação **não-galeria.**
3. Na secção **Propriedades** da sua nova aplicação personalizada, copie o ID do **Objeto**.
4. Numa nova janela do navegador https://developer.microsoft.com/graph/graph-explorer web, vá e inscreva-se como administrador do inquilino Azure AD onde a sua aplicação é adicionada.
5. No Graph Explorer, execute o comando abaixo para inicializar a configuração de provisionamento da sua aplicação.
   Substitua "[object-id]" pelo ID do diretor de serviço (ID do objeto) copiado a partir do terceiro passo.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Volte à primeira janela do navegador web e selecione o separador **Provisioning** para a sua aplicação.
7. Complete a configuração de fornecimento do utilizador como normalmente faria.


## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o provisionamento e o desprovisionamento às aplicações saaS](user-provisioning.md)

