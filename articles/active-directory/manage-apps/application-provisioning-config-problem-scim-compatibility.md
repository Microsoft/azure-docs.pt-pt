---
title: Problemas conhecidos com a conformidade do protocolo SCIM 2,0-Azure AD
description: Como resolver problemas comuns de compatibilidade de protocolo enfrentados ao adicionar um aplicativo inexistente na galeria que dá suporte a SCIM 2,0 ao Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: eee480d4a52f77e054bf8f0780707444b6db28b0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275805"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Problemas conhecidos e resoluções com a conformidade do protocolo SCIM 2,0 do serviço de provisionamento de usuários do Azure AD

O Azure Active Directory (AD do Azure) pode provisionar automaticamente usuários e grupos para qualquer aplicativo ou sistema que seja administrado por um serviço Web com a interface definida no [sistema para especificação de protocolo scim (gerenciamento de identidade entre domínios) 2,0](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

O suporte do Azure AD para o protocolo SCIM 2,0 é descrito em [usando o sistema para scim (gerenciamento de identidade entre domínios) para provisionar automaticamente usuários e grupos de Azure Active Directory para aplicativos](use-scim-to-provision-users-and-groups.md), que lista as partes específicas do protocolo que ele implementa para provisionar automaticamente usuários e grupos do Azure ad para aplicativos que dão suporte a scim 2,0.

Este artigo descreve os problemas atuais e antigos com a adesão do serviço de provisionamento de usuários do Azure AD ao protocolo SCIM 2,0 e como contornar esses problemas.

> [!IMPORTANT]
> A atualização mais recente do cliente SCIM do serviço de provisionamento de usuários do Azure AD foi feita em 18 de dezembro de 2018. Essa atualização resolveu os problemas de compatibilidade conhecidos listados na tabela a seguir. Consulte as perguntas frequentes abaixo para obter mais informações sobre esta atualização.

## <a name="scim-20-compliance-issues-and-status"></a>Problemas de conformidade e status do SCIM 2,0

| **Problema de conformidade do SCIM 2,0** |  **Fixado?** | **Corrigir data**  |  
|---|---|---|
| O Azure AD exige que "/SCIM" esteja na raiz da URL do ponto de extremidade SCIM do aplicativo  | Sim  |  18 de dezembro de 2018 | 
| Atributos de extensão usam notação de ponto "." antes dos nomes de atributo em vez de dois-pontos ":". notação |  Sim  | 18 de dezembro de 2018  | 
|  Solicitações de patch para atributos de vários valores contêm sintaxe de filtro de caminho inválida | Sim  |  18 de dezembro de 2018  | 
|  As solicitações de criação de grupo contêm um URI de esquema inválido | Sim  |  18 de dezembro de 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>As correções de serviços foram descritas automaticamente aplicadas ao meu aplicativo SCIM já existente?

Não. Como teria sido constituído por uma alteração significativa nos aplicativos SCIM que eram codificados para funcionar com o comportamento mais antigo, as alterações não foram aplicadas automaticamente a aplicativos existentes.

As alterações são aplicadas a todos os novos [aplicativos de SCIM não Galeria](configure-single-sign-on-non-gallery-applications.md) configurados no portal do Azure, após a data da correção.

Para obter informações sobre como migrar um trabalho de provisionamento de usuário pré-existente para incluir as correções mais recentes, consulte a próxima seção.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Posso migrar um trabalho de provisionamento de usuário existente baseado em SCIM para incluir as correções de serviço mais recentes?

Sim. Se você já estiver usando essa instância de aplicativo para logon único e precisar migrar o trabalho de provisionamento existente para incluir as correções mais recentes, siga o procedimento abaixo. Este procedimento descreve como usar a API do Microsoft Graph e o Gerenciador de API do Microsoft Graph para remover seu trabalho de provisionamento antigo de seu aplicativo SCIM existente e criar um novo que exiba o novo comportamento.

> [!NOTE]
> Se seu aplicativo ainda estiver em desenvolvimento e ainda não tiver sido implantado para logon único ou provisionamento de usuário, a solução mais fácil será excluir a entrada do aplicativo na seção **Azure Active Directory > aplicativos empresariais** do portal do Azure e simplesmente adicionar uma nova entrada para o aplicativo usando a opção **criar aplicativo > não Galeria** . Essa é uma alternativa à execução do procedimento abaixo.
 
1. Entre no portal do Azure em https://portal.azure.com.
2. Na seção **Azure Active Directory > aplicativos empresariais** do portal do Azure, localize e selecione o aplicativo scim existente.
3. Na seção **Propriedades** do aplicativo scim existente, copie a ID de **objeto**.
4. Em uma nova janela do navegador da Web, acesse https://developer.microsoft.com/graph/graph-explorer e entre como o administrador do locatário do Azure AD em que seu aplicativo é adicionado.
5. No Gerenciador de gráficos, execute o comando a seguir para localizar a ID do seu trabalho de provisionamento. Substitua "[ID-do-objeto]" pela ID da entidade de serviço (ID de objeto) copiada da terceira etapa.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Obter trabalhos](./media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Obter trabalhos") 


6. Nos resultados, copie a cadeia de caracteres "ID" completa que começa com "customappsso" ou "SCIM".
7. Execute o comando a seguir para recuperar a configuração de mapeamento de atributo, para que você possa fazer um backup. Use o mesmo [Object-ID] como antes e substitua [job-id] pela ID do trabalho de provisionamento copiada da última etapa.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Obter esquema](./media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Obter esquema") 

8. Copie a saída JSON da última etapa e salve-a em um arquivo de texto. Ele contém qualquer mapeamento de atributo personalizado que você adicionou ao seu aplicativo antigo e deve ter aproximadamente algumas milhares de linhas de JSON.
9. Execute o comando a seguir para excluir o trabalho de provisionamento:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Execute o comando a seguir para criar um novo trabalho de provisionamento que tenha as correções de serviço mais recentes.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Nos resultados da última etapa, copie a cadeia de caracteres "ID" completa que começa com "SCIM". Opcionalmente, aplique novamente os mapeamentos de atributo antigos executando o comando a seguir, substituindo [New-Job-ID] pela nova ID do trabalho que você acabou de copiar e inserindo a saída JSON da etapa #7 como o corpo da solicitação.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Volte para a primeira janela do navegador da Web e selecione a guia **provisionamento** para seu aplicativo.
13. Verifique sua configuração e inicie o trabalho de provisionamento. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Posso adicionar um novo aplicativo que não seja da galeria que tem o comportamento de provisionamento do usuário antigo?

Sim. Se você tiver codificado um aplicativo para o comportamento antigo que existia antes das correções e precisar implantar uma nova instância dele, siga o procedimento abaixo. Este procedimento descreve como usar a API do Microsoft Graph e o Gerenciador de API do Microsoft Graph para criar um trabalho de provisionamento do SCIM que exibe o comportamento antigo.
 
1. Entre no portal do Azure em https://portal.azure.com.
2. na seção **Azure Active Directory > aplicativos empresariais > criar aplicativo** do portal do Azure, crie um novo aplicativo **que não seja da Galeria** .
3. Na seção **Propriedades** do seu novo aplicativo personalizado, copie a **ID do objeto**.
4. Em uma nova janela do navegador da Web, acesse https://developer.microsoft.com/graph/graph-explorer e entre como o administrador do locatário do Azure AD em que seu aplicativo é adicionado.
5. No Gerenciador de gráficos, execute o comando a seguir para inicializar a configuração de provisionamento para seu aplicativo.
   Substitua "[ID-do-objeto]" pela ID da entidade de serviço (ID de objeto) copiada da terceira etapa.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Volte para a primeira janela do navegador da Web e selecione a guia **provisionamento** para seu aplicativo.
7. Conclua a configuração de provisionamento do usuário normalmente.


## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o provisionamento e desprovisionamento para aplicativos SaaS](user-provisioning.md)

