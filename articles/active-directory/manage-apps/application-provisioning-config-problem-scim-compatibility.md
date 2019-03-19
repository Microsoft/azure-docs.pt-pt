---
title: Conhecidos de problemas e resoluções com SCIM 2.0 compatibilidade de protocolo do serviço aprovisionamento de utilizador do Azure AD | Documentos da Microsoft
description: Como pode resolver problemas de compatibilidade de protocolo comuns enfrentados ao adicionar uma aplicação de externas à galeria que suporta SCIM 2.0 para o Azure AD
services: active-directory
documentationcenter: ''
author: asmalser
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fc326c1ba529bc394a5ce5a059e3fe91baa7a9a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124088"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Problemas conhecidos e resoluções com SCIM 2.0 compatibilidade de protocolo do serviço aprovisionamento de utilizador do Azure AD

Azure Active Directory (Azure AD), pode aprovisionar automaticamente os utilizadores e grupos para qualquer aplicativo ou sistema que é apoiado por um serviço da web com a interface definidos na [sistema para o protocolo de gestão de identidade entre domínios (SCIM) 2.0 especificação](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Suporte do Azure AD para o protocolo de SCIM 2.0 é descrito em [usando o sistema de gestão de identidade de entre domínios (SCIM) para aprovisionar automaticamente os utilizadores e grupos do Azure Active Directory a aplicações](use-scim-to-provision-users-and-groups.md), que apresenta uma lista a obter partes específicas do protocolo que implementa para aprovisionar automaticamente os utilizadores e grupos do Azure AD para aplicações que suportam SCIM 2.0.

Este artigo descreve problemas atuais e anteriores, com o utilizador do Azure AD a aderência do serviço para o protocolo SCIM 2.0 e como solucionar esses problemas de aprovisionamento.

> [!IMPORTANT]
> A atualização mais recente para o cliente SCIM aprovisionamento do serviço do Azure AD utilizador foi efetuada a 18 de Dezembro de 2018. Esta atualização, resolvido os problemas de compatibilidade conhecidos listados na tabela abaixo. Consulte as perguntas mais frequentes sobre abaixo para obter mais informações sobre esta atualização.

## <a name="scim-20-compliance-issues-and-status"></a>Problemas de conformidade de SCIM 2.0 e o Estado

| **Problema de conformidade de SCIM 2.0** |  **Corrigido?** | **Corrigir a data**  |  
|---|---|---|
| Azure AD requer "/ scim" para ser na raiz do aplicativo do URL de ponto final SCIM  | Sim  |  18 de Dezembro de 2018 | 
| Atributos de extensão utilizam ponto "."notação antes dos nomes de atributo em vez de dois pontos":" notação |  Sim  | 18 de Dezembro de 2018  | 
|  Os pedidos de patch para atributos de valores múltiplos contêm sintaxe de filtro de caminho inválido | Sim  |  18 de Dezembro de 2018  | 
|  Pedidos de criação do grupo contenham um URI de esquema inválido | Sim  |  18 de Dezembro de 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Foram as correções de serviços descritas aplicadas automaticamente ao meu aplicativo SCIM já existente?

Não. Como ele seria ter constituíram uma alteração de última hora para aplicações SCIM que tivesse sido codificado para trabalhar com o comportamento mais antigo, as alterações não foram aplicadas automaticamente para aplicações existentes.

As alterações são aplicadas a todos os novos [externas à Galeria SCIM aplicações](configure-single-sign-on-non-gallery-applications.md) configurado no portal do Azure, após a data da correção.

Para obter informações sobre como migrar de um utilizador já existente aprovisionar a tarefa para incluir as correções mais recentes, consulte a secção seguinte.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Posso migrar um existente com base em SCIM utilizador aprovisionar a tarefa para incluir as correções mais recentes do serviço?

Sim. Se já estiver a utilizar esta instância da aplicação para início de sessão único e precisa de migrar a tarefa de aprovisionamento existente para incluir as correções mais recentes, siga o procedimento abaixo. Este procedimento descreve como utilizar o Microsoft Graph API e o Explorador do Microsoft Graph API para remover o seu trabalho de aprovisionamento antigo da sua aplicação SCIM existente e criar um novo que exibe o novo comportamento.

> [!NOTE]
> Se seu aplicativo ainda está em desenvolvimento e ainda não foi implementado para o início de sessão único ou o aprovisionamento de utilizadores, a solução mais simples consiste em eliminar a entrada de aplicativo no **do Azure Active Directory > aplicações empresariais**seção do portal do Azure e simplesmente adicionar uma nova entrada para a aplicação utilizando o **Criar aplicação > Galeria não** opção. Esta é uma alternativa ao executar o procedimento abaixo.
 
1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Na **do Azure Active Directory > aplicações empresariais** seção do portal do Azure, localize e selecione a sua aplicação SCIM existente.
3. No **propriedades** secção da sua aplicação SCIM existente, copie a **ID de objeto**.
4. Na nova janela do browser web, aceda a https://developer.microsoft.com/graph/graph-explorer e inicie sessão como administrador de inquilino do Azure AD em que a sua aplicação é adicionada.
5. No Explorador do gráfico, execute o comando abaixo para localizar o ID da tarefa de aprovisionamento. Substitua o ID de principal (ID de objeto) copiado da terceira etapa de serviço "[object-id]".
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Obter tarefas](./media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "obter tarefas") 


6. Nos resultados, copie a cadeia de caracteres completa "ID" que começa com "customappsso" ou "scim".
7. Execute o comando abaixo para obter a configuração de mapeamento do atributo, por isso, pode fazer uma cópia de segurança. Utilize o mesmo [-id de objeto] como antes e substitua [id da tarefa] com o ID de tarefa de aprovisionamento copiado do último passo.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Obter esquema](./media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "obter esquema") 

8. Copie a saída JSON do último passo e guarde-o para um arquivo de texto. Contém qualquer personalizados-mapeamentos de atributos adicionados ao seu aplicativo antigo e deve ser aproximadamente alguns milhares de linhas de JSON.
9. Execute o comando abaixo para eliminar a tarefa de aprovisionamento:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Execute o comando abaixo para criar uma nova tarefa de aprovisionamento que tem as correções mais recentes do serviço.

    `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs `
    `{   templateId: "scim"   } `
   
11. Nos resultados da última etapa, copie a cadeia de caracteres completa "ID" que começa com "scim". Opcionalmente, volte a aplicar seus mapeamentos de atributos antigos ao executar o comando abaixo, substituindo [novo--id da tarefa] com o novo ID de tarefa que acabou de ser copiada e introduzir que o JSON de saída do passo 7 de # como o corpo do pedido.

    `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema `
    `{   <your-schema-json-here>   }`

12. Devolver a primeira janela do browser e selecione o **aprovisionamento** separador para a sua aplicação.
13. Verificar a configuração e, em seguida, iniciar a tarefa de aprovisionamento. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Pode adicionar uma nova aplicação de externas à galeria que tem o comportamento de aprovisionamento de utilizador antigo?

Sim. Se tivesse codificou uma aplicação ao comportamento antigo que existia antes das correções e tem de implementar uma nova instância do mesmo, siga o procedimento abaixo. Este procedimento descreve como utilizar o Microsoft Graph API e o Explorador do Microsoft Graph API para criar uma tarefa de aprovisionamento SCIM que exiba o comportamento antigo.
 
1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. na **do Azure Active Directory > aplicações empresariais > Criar aplicação** secção do portal do Azure, crie uma nova **Galeria não** aplicação.
3. No **propriedades** secção da sua nova aplicação personalizada, copie a **ID de objeto**.
4. Na nova janela do browser web, aceda a https://developer.microsoft.com/graph/graph-explorer e inicie sessão como administrador de inquilino do Azure AD em que a sua aplicação é adicionada.
5. No Explorador do gráfico, execute o comando abaixo para inicializar a configuração de aprovisionamento para a sua aplicação.
   Substitua o ID de principal (ID de objeto) copiado da terceira etapa de serviço "[object-id]".

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Devolver a primeira janela do browser e selecione o **aprovisionamento** separador para a sua aplicação.
7. Conclua o configuração de provisionamento, tal como faria normalmente de usuários.


## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre o aprovisionamento e cancelamento de aprovisionamento a aplicações SaaS](user-provisioning.md)

