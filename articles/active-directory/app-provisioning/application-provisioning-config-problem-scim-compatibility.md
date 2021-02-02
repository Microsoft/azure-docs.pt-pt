---
title: Problemas conhecidos com o System for Cross-Domain Identity Management (SCIM) 2.0 protocol compliance - Azure AD
description: Como resolver problemas comuns de compatibilidade do protocolo enfrentados ao adicionar uma aplicação não-galeria que suporta o SCIM 2.0 a AD Azure
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 08/05/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: d13629b4cb05995b9652e862f769a0ffcae30a8c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256903"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Questões e resoluções conhecidas com o protocolo SCIM 2.0 cumprimento do serviço de fornecimento de utilizadores Azure AD

O Azure Ative Directory (Azure AD) pode automaticamente forneça utilizadores e grupos a qualquer aplicação ou sistema que seja frontalizado por um serviço web com a interface definida no [Sistema de Gestão de Identidade de Domínio Cruzado (SCIM) 2.0 especificação do protocolo](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

O suporte da AZure AD para o protocolo SCIM 2.0 é descrito na [Utilização do Sistema de Gestão de Identidade de Domínio Cruzado (SCIM) para fornecer automaticamente utilizadores e grupos do Azure Ative Directory às aplicações](use-scim-to-provision-users-and-groups.md), que lista as partes específicas do protocolo que implementa de forma a fornecer automaticamente utilizadores e grupos de Azure AD a aplicações que suportam o SCIM 2.0.

Este artigo descreve problemas atuais e passados com a adesão do serviço de fornecimento de utilizadores Azure AD ao protocolo SCIM 2.0 e como contornar estas questões.

## <a name="understanding-the-provisioning-job"></a>Compreender o trabalho de provisionamento
O serviço de prestação utiliza o conceito de trabalho para operar contra uma aplicação. O trabalhoID pode ser encontrado na [barra de progresso.](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) Todas as novas aplicações de provisionamento são criadas com um trabalhoID começando com "scim". O trabalho de cim representa o estado atual do serviço. Os trabalhos mais antigos têm a identificação "customappsso". Este trabalho representa o estado do serviço em 2018. 

Se estiver a utilizar uma aplicação na galeria, o trabalho geralmente contém o nome da aplicação (por exemplo, zoom snowFlake, dataBricks, etc.). Pode ignorar esta documentação quando utilizar uma aplicação de galeria. Isto aplica-se principalmente a aplicações não-galeria com o jobID SCIM ou o CustomAppSSO.

## <a name="scim-20-compliance-issues-and-status"></a>PROBLEMAS DE CONFORMIDADE SCIM 2.0 e estado
Na tabela abaixo, qualquer item marcado como fixo significa que o comportamento adequado pode ser encontrado no trabalho scim. Trabalhámos para assegurar a retrocompatibilidade das alterações que fizemos. No entanto, não recomendamos a implementação de comportamentos antigos. Recomendamos a utilização do novo comportamento para quaisquer novas implementações e atualização das implementações existentes.

> [!NOTE]
> Para as alterações feitas em 2018, pode voltar ao comportamento personalizado. Para as alterações feitas desde 2018, pode usar os URLs para voltar ao comportamento mais antigo. Trabalhámos para garantir a retrocompatibilidade para as alterações que fizemos, permitindo-lhe voltar ao antigo trabalhoID ou usando uma bandeira. No entanto, como mencionado anteriormente, não recomendamos a implementação de comportamentos antigos. Recomendamos a utilização do novo comportamento para quaisquer novas implementações e atualização das implementações existentes.

| **Emissão de conformidade SCIM 2.0** |  **Fixo?** | **Data de fixação**  |  **Compatibilidade ao contrário** |
|---|---|---|
| A AD AZure exige que "/scim" esteja na raiz do URL do ponto final scim da aplicação  | Sim  |  18 de dezembro de 2018 | downgrade para customappSSO |
| Atributos de extensão usam ponto "." notação antes de atribuir nomes em vez de cólon ":" notação |  Sim  | 18 de dezembro de 2018  | downgrade para customappSSO |
| Os pedidos de remendos para atributos de vários valores contêm sintaxe de filtro de caminho inválido | Sim  |  18 de dezembro de 2018  | downgrade para customappSSO |
| Os pedidos de criação de grupo contêm um esquema inválido URI | Sim  |  18 de dezembro de 2018  |  downgrade para customappSSO |
| Atualizar o comportamento do PATCH para garantir a conformidade (por exemplo, ativa como remoção de membros booleanos e adequados) | Não | TBD| usar bandeira de pré-visualização |

## <a name="flags-to-alter-the-scim-behavior"></a>Bandeiras para alterar o comportamento do SCIM
Use as bandeiras abaixo no URL do inquilino da sua aplicação para alterar o comportamento padrão do cliente SCIM.

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.jpg" alt-text="Scim bandeiras para comportamento posterior.":::

* Utilize o seguinte URL para atualizar o comportamento do PATCH e garantir a conformidade do SCIM (por exemplo, ativo como remoção de membros booleanos e adequados para membros do grupo). Este comportamento está atualmente disponível apenas quando se usa a bandeira, mas tornar-se-á o comportamento padrão ao longo dos próximos meses. Note que esta bandeira de pré-visualização não funciona atualmente com o provisionamento a pedido. 
  * **URL (Conformidade COM a SCIM):** AzureAdScimPatch062020
  * **Referências SCIM RFC:** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2
  * **Comportamento:**
  ```json
   PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
   {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "remove",
            "path": "members[value eq \"16b083c0-f1e8-4544-b6ee-27a28dc98761\"]"
        }
    ]
   }

    PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "members",
            "value": [
                {
                    "value": "10263a6910a84ef9a581dd9b8dcc0eae"
                }
            ]
        }
    ]
    } 

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].value",
            "value": "someone@contoso.com"
        },
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].primary",
            "value": true
        },
        {
            "op": "replace",
            "value": {
                "active": false,
                "userName": "someone"
            }
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "active",
            "value": false
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department",
            "value": "Tech Infrastructure"
        }
    ]
    }
   
  ```

  * **URL de downgrade:** Uma vez que o novo comportamento em conformidade com o SCIM se torne o padrão na aplicação não-galeria, você pode usar o seguinte URL para reverter para o comportamento antigo, não compatível COM SCIM: AzureAdScimPatch2017
  


## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>Upgrade do trabalho personalizado mais antigo para o trabalho SCIM
Seguindo os passos abaixo irá eliminar o seu trabalho de aplicação personalizada existente e criar um novo trabalho de scim. 
 
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

8. Copie a saída JSON do último passo e guarde-a num ficheiro de texto. O JSON contém quaisquer mapeamentos de atributos personalizados que adicionou à sua antiga aplicação, e deve ser aproximadamente alguns milhares de linhas de JSON.
9. Executar o comando abaixo para eliminar o trabalho de provisionamento:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Executar o comando abaixo para criar um novo trabalho de provisionamento que tenha as mais recentes correções de serviço.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Nos resultados do último passo, copie a cadeia completa de "ID" que começa com "scim". Opcionalmente, reaplicar os seus antigos mapeamentos de atributos executando o comando abaixo, substituindo [id de novo emprego] pelo novo iD de trabalho que copiou, e introduzindo a saída JSON a partir do passo #7 como o órgão de pedido.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Volte à primeira janela do navegador web e selecione o **separador Provisioning** para a sua aplicação.
13. Verifique a sua configuração e, em seguida, inicie o trabalho de provisionamento. 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>Downgrade do trabalho scim para o trabalho personalizadoappsso (não recomendado)
 Permitimos que você recue para o comportamento antigo, mas não o recomende, uma vez que o personalappsso não beneficia de algumas das atualizações que fazemos, e pode não ser suportado para sempre. 

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


## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o provisionamento e desavisionamento das aplicações do SaaS](user-provisioning.md)
