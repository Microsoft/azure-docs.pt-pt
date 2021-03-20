---
title: Como gerir o serviço de certificados OPC Vault - Azure | Microsoft Docs
description: Gerir os certificados de CA de raiz de opc vault e permissões do utilizador.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0829d4b3fca068ddb0db2df53dd635ab7ad80bed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91281920"
---
# <a name="manage-the-opc-vault-certificate-service"></a>Gerir o serviço de certificados OPC Vault

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Este artigo explica as tarefas administrativas do serviço de gestão de certificados OPC Vault em Azure. Inclui informações sobre como renovar os certificados de EMER CA, como renovar a Lista de Revogação de Certificados (CRL), e como conceder e revogar o acesso ao utilizador.

## <a name="create-or-renew-the-root-ca-certificate"></a>Criar ou renovar o certificado de CA raiz

Depois de implantar o Cofre OPC, deve criar o certificado de CA raiz. Sem um certificado de EMITENTE válido, não pode assinar ou emitir certificados de inscrição. Consulte [os Certificados](howto-opc-vault-secure-ca.md#certificates) para gerir os seus certificados com uma vida útil razoável e segura. Renove um certificado de Emitente ac após metade da sua vida. Ao renovar, considere também que a vida útil configurada de um certificado de candidatura recentemente assinado não deve exceder o tempo de vida útil do certificado emitente ca.
> [!IMPORTANT]
> A função de Administrador é necessária para criar ou renovar o certificado emitente CA.

1. Abra o seu serviço de certificados `https://myResourceGroup-app.azurewebsites.net` em, e inscreva-se.
2. Ir para **grupos de certificados.**
3. Há um grupo de certificados por defeito listado. Selecione **Editar**.
4. Nos **Detalhes do Grupo de Certificados de Edição,** pode modificar o nome do assunto e o tempo de vida útil dos seus certificados de ca e de candidatura. O sujeito e as vidas só devem ser definidos uma vez antes da emissão do primeiro certificado de CA. As alterações ao longo da vida durante as operações podem resultar em períodos de vida inconsistentes de certificados emitidos e DERs.
5. Introduza um assunto válido (por exemplo, `CN=My CA Root, O=MyCompany, OU=MyDepartment` ).<br>
   > [!IMPORTANT]
   > Se alterar o assunto, deverá renovar o certificado emitente, ou o serviço deixará de assinar certificados de inscrição. O objeto da configuração é verificado com o objeto do certificado emitente ativo. Se os sujeitos não corresponderem, a assinatura do certificado é recusada.
6. Selecione **Guardar**.
7. Se encontrar um erro "proibido" neste momento, as credenciais do utilizador não têm a permissão do administrador para modificar ou criar um novo certificado de raiz. Por predefinição, o utilizador que implementou o serviço tem funções de administrador e de assinatura com o serviço. Outros utilizadores devem ser adicionados às funções de Approver, Writer ou Administrator, conforme apropriado no registo de candidaturas Azure Ative (Azure AD).
8. Selecione **Detalhes**. Isto deve mostrar a informação atualizada.
9. Selecione **Renovar o Certificado CA** para emitir o primeiro certificado de EMITENTE CA ou para renovar o certificado emitente. Em seguida, selecione **OK**.
10. Após alguns segundos, verá detalhes do **certificado.** Para baixar o mais recente certificado ca e CRL para distribuição nas suas aplicações OPC UA, selecione **Emitente** ou **Crl**.

Agora, o serviço de gestão de certificados da OPC UA está pronto para emitir certificados para aplicações OPC UA.

## <a name="renew-the-crl"></a>Renovar o CRL

A renovação do CRL é uma atualização, que deve ser distribuída às aplicações a intervalos regulares. Os dispositivos OPC UA, que suportam a extensão CRL Distribution Point X509, podem atualizar diretamente o CRL a partir do ponto final do microserviço. Outros dispositivos OPC UA podem necessitar de atualizações manuais, ou podem ser atualizados utilizando extensões de impulso do servidor GDS (*) para atualizar as listas de confiança com os certificados e CRLs.

No seguinte fluxo de trabalho, todos os pedidos de certificado nos estados eliminados são revogados nos CRLs, que correspondem ao certificado de EMEIR CA para o qual foram emitidos. O número de versão do CRL é aumentado por 1. <br>
> [!NOTE]
> Todos os CRLs emitidos são válidos até ao termo do certificado emitente CA. Isto porque a especificação da UA OPC não requer um modelo de distribuição determinista obrigatório para a CRL.

> [!IMPORTANT]
> A função de administrador é necessária para renovar o Emitente CRL.

1. Abra o seu serviço de certificados `https://myResourceGroup.azurewebsites.net` em, e inscreva-se.
2. Aceda à página **grupos de certificados.**
3. Selecione **Detalhes**. Isto deve mostrar o certificado atual e as informações de CRL.
4. Selecione **Update CRL Revocation List (CRL)** para emitir um CRL atualizado para todos os certificados emitentes ativos no armazenamento do Cofre OPC.
5. Após alguns segundos, verá detalhes do **certificado.** Para baixar o mais recente certificado ca e CRL para distribuição nas suas aplicações OPC UA, selecione **Emitente** ou **Crl**.

## <a name="manage-user-roles"></a>Gerir funções de utilizador

Gere as funções de utilizador para o microservice OPC Vault na Aplicação AD AD da Azure. Para obter uma descrição detalhada das definições de funções, consulte [As Funções](howto-opc-vault-secure-ca.md#roles).

Por predefinição, um utilizador autenticado no arrendatário pode assinar no serviço como Leitor. Funções privilegiadas superiores requerem gestão manual no portal Azure, ou utilizando o PowerShell.

### <a name="add-user"></a>Adicionar utilizador

1. Abra o portal do Azure.
2. Aceda às aplicações **da Azure Ative Directory**  >  **Enterprise**.
3. Escolha o registo do microserviço OPC Vault (por defeito, o `resourceGroupName-service` seu).
4. Ir aos **Utilizadores e Grupos.**
5. Selecione **Adicionar Utilizador**.
6. Selecione ou convide o utilizador para a atribuição para uma função específica.
7. Selecione a função para os utilizadores.
8. Selecione **Atribuir**.
9. Para os utilizadores na função Administrador ou Aprovação, continue a adicionar políticas de acesso a Azure Key Vault.

### <a name="remove-user"></a>Remover utilizador

1. Abra o portal do Azure.
2. Aceda às aplicações **da Azure Ative Directory**  >  **Enterprise**.
3. Escolha o registo do microserviço OPC Vault (por defeito, o `resourceGroupName-service` seu).
4. Ir aos **Utilizadores e Grupos.**
5. Selecione um utilizador com uma função a remover e, em seguida, **selecione Remover**.
6. Para os utilizadores removidos na função Administrador ou Aprovação, remova-os também das políticas do Azure Key Vault.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Adicione a política de acesso ao cofre de chaves Azure

São necessárias políticas de acesso adicionais para os Aprovadores e Administradores.

Por predefinição, a identidade do serviço tem apenas permissões limitadas para aceder ao Key Vault, para evitar operações elevadas ou alterações a ocorrer sem a representação do utilizador. As permissões básicas de serviço são Get and List, tanto para segredos como certificados. Para segredos, há apenas uma exceção: o serviço pode apagar uma chave privada da loja secreta depois de ser aceite por um utilizador. Todas as outras operações requerem permissões de imitação do utilizador.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Para um papel de Aprovação, as seguintes permissões devem ser adicionadas ao Cofre-Chave

1. Abra o portal do Azure.
2. Vá ao seu Cofre `resourceGroupName` OPC, utilizado durante a implantação.
3. Vai para o Cofre das `resourceGroupName-xxxxx` Chaves.
4. Ir para **políticas de acesso.**
5. **Selecione Adicionar novo**.
6. Ignore o modelo. Não há modelo que corresponda aos requisitos.
7. Escolha **Select Principal** e selecione o utilizador a ser adicionado, ou convide um novo utilizador para o inquilino.
8. Selecione as **seguintes permissões chave**: **Obter,** **Lista** e **Assinar**.
9. Selecione as **seguintes permissões secretas**: **Obter,** **Lista,** **Definir** e **Excluir**.
10. Selecione as **seguintes permissões de Certificado**: **Obter** e **Listar**.
11. Selecione **OK**, e selecione **Guardar**.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Para uma função de administrador, as seguintes permissões devem ser adicionadas ao Cofre-Chave

1. Abra o portal do Azure.
2. Vá ao seu Cofre `resourceGroupName` OPC, utilizado durante a implantação.
3. Vai para o Cofre das `resourceGroupName-xxxxx` Chaves.
4. Ir para **políticas de acesso.**
5. **Selecione Adicionar novo**.
6. Ignore o modelo. Não há modelo que corresponda aos requisitos.
7. Escolha **Select Principal** e selecione o utilizador a ser adicionado, ou convide um novo utilizador para o inquilino.
8. Selecione as **seguintes permissões chave**: **Obter,** **Lista** e **Assinar**.
9. Selecione as **seguintes permissões secretas**: **Obter,** **Lista,** **Definir** e **Excluir**.
10. Selecione as **seguintes permissões de Certificado**: **Obter,** **Listar,** **Atualizar,** **Criar** e **Importar**.
11. Selecione **OK**, e selecione **Guardar**.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Remover a política de acesso ao utilizador do Cofre de Chaves Azure

1. Abra o portal do Azure.
2. Vá ao seu Cofre `resourceGroupName` OPC, utilizado durante a implantação.
3. Vai para o Cofre das `resourceGroupName-xxxxx` Chaves.
4. Ir para **políticas de acesso.**
5. Encontre o utilizador para remover e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir certificados e utilizadores do OPC Vault, pode:

> [!div class="nextstepaction"]
> [Comunicação segura de dispositivos OPC](howto-opc-vault-secure.md)
