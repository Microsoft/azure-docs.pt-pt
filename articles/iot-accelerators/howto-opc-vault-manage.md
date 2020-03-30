---
title: Como gerir o serviço de certificado saque oPc Vault - Azure / Microsoft Docs
description: Gerencie os certificados ca raiz do Cofre OPC e permissões do utilizador.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71203664"
---
# <a name="manage-the-opc-vault-certificate-service"></a>Gerir o serviço de certificado saque opc

Este artigo explica as tarefas administrativas para o serviço de gestão de certificados OPC Vault em Azure. Inclui informações sobre como renovar os certificados CA emitentes, como renovar a Lista de Revogação de Certificados (CRL) e como conceder e revogar o acesso ao utilizador.

## <a name="create-or-renew-the-root-ca-certificate"></a>Criar ou renovar o certificado ca raiz

Depois de implantar o Cofre OPC, deve criar o certificado ca raiz. Sem um certificado CA emitente válido, não pode assinar ou emitir certificados de candidatura. Consulte [os Certificados](howto-opc-vault-secure-ca.md#certificates) para gerir os seus certificados com vidas razoáveis e seguras. Renovar um certificado de Emitente CA após metade da sua vida útil. Ao renovar, considere também que a vida útil configurada de um certificado de candidatura recém-assinado não deve exceder o tempo de vida útil do certificado CA emitente.
> [!IMPORTANT]
> A função de Administrador é necessária para criar ou renovar o certificado CA emitente.

1. Abra o seu `https://myResourceGroup-app.azurewebsites.net`serviço de certificados em, e inscreva-se.
2. Ir para Grupos de **Certificados.**
3. Há um grupo de certificados predefinidolistado. Selecione **Editar**.
4. Em Dados do **Grupo de Certificados de Edição,** pode modificar o nome e a vida útil dos seus certificados ca e de aplicação. O sujeito e as vidas só devem ser definidos uma vez antes da emissão do primeiro certificado ca. As alterações ao longo da vida durante as operações podem resultar em períodos de vida inconsistentes de certificados emitidos e LL.
5. Introduza um assunto válido `CN=My CA Root, O=MyCompany, OU=MyDepartment`(por exemplo, ).<br>
   > [!IMPORTANT]
   > Se alterar o assunto, tem de renovar o certificado Emitente, ou o serviço não assinará certificados de inscrição. O objeto da configuração é verificado contra o objeto do certificado emitente ativo. Se os sujeitos não corresponderem, a assinatura do certificado é recusada.
6. Selecione **Guardar**.
7. Se encontrar um erro "proibido" neste momento, as credenciais do utilizador não têm autorização do administrador para modificar ou criar um novo certificado raiz. Por predefinição, o utilizador que implementou o serviço tem funções de administrador e de assinatura com o serviço. Outros utilizadores devem ser adicionados às funções de Aprovador, Escritor ou Administrador, conforme apropriado no registo de aplicação azure Ative Directory (Azure AD).
8. Selecione **Detalhes**. Isto deve mostrar a informação atualizada.
9. **Selecione Renovar o Certificado CA** para emitir o primeiro certificado CA emitente ou para renovar o certificado emitente. Em seguida, selecione **OK**.
10. Após alguns segundos, verá **os Dados do Certificado.** Para descarregar o mais recente certificado CA e CRL para distribuição nas suas aplicações OPC UA, selecione **Emitente** ou **Crl**.

Agora, o serviço de gestão de certificados OPC UA está pronto para emitir certificados para aplicações da OPC UA.

## <a name="renew-the-crl"></a>Renovar o CRL

A renovação do CRL é uma atualização, que deve ser distribuída às aplicações a intervalos regulares. Os dispositivos OPC UA, que suportam a extensão CRL Distribution Point X509, podem atualizar diretamente o CRL a partir do ponto final do microserviço. Outros dispositivos OPC UA podem necessitar de atualizações manuais, ou podem ser atualizados utilizando extensões de pressão do servidor GDS (*) para atualizar as listas de confiança com os certificados e CRLs.

No fluxo de trabalho seguinte, todos os pedidos de certificadonos estados eliminados são revogados nos CRLs, que correspondem ao certificado CA emitente para o qual foram emitidos. O número de versão do CRL é incrementado por 1. <br>
> [!NOTE]
> Todos os CRLs emitidos são válidos até à expiração do certificado CA emitente. Isto porque a especificação da OPC UA não requer um modelo de distribuição determinista obrigatório para a CRL.

> [!IMPORTANT]
> A função de Administrador é necessária para renovar o CRL emitente.

1. Abra o seu `https://myResourceGroup.azurewebsites.net`serviço de certificados em, e inscreva-se.
2. Vá à página dos **Grupos de Certificados.**
3. Selecione **Detalhes**. Isto deve mostrar o certificado atual e a informação de CRL.
4. Selecione **Update CRL Revocation List (CRL)** para emitir um CRL atualizado para todos os certificados emitentes ativos no armazenamento do Cofre opc.
5. Após alguns segundos, verá **os Dados do Certificado.** Para descarregar o mais recente certificado CA e CRL para distribuição nas suas aplicações OPC UA, selecione **Emitente** ou **Crl**.

## <a name="manage-user-roles"></a>Gerir funções de utilizador

Gere as funções de utilizador do microserviço OPC Vault na Aplicação Empresarial Azure AD. Para uma descrição detalhada das definições de papel, consulte [Funções](howto-opc-vault-secure-ca.md#roles).

Por predefinição, um utilizador autenticado no inquilino pode assinar no serviço como Leitor. Funções privilegiadas mais elevadas requerem gestão manual no portal Azure, ou utilizando o PowerShell.

### <a name="add-user"></a>Adicionar utilizador

1. Abra o portal do Azure.
2. Vá às aplicações **da Azure Ative Directory** > **Enterprise.**
3. Escolha o registo do microserviço OPC Vault `resourceGroupName-service`(por defeito, o seu).
4. Ir para **Utilizadores e Grupos**.
5. Selecione **Adicionar utilizador**.
6. Selecione ou convide o utilizador para uma função específica.
7. Selecione a função para os utilizadores.
8. Selecione **Atribuir**.
9. Para os utilizadores na função De Administrador ou Aprovador, continue a adicionar políticas de acesso ao Cofre chave Azure.

### <a name="remove-user"></a>Remover utilizador

1. Abra o portal do Azure.
2. Vá às aplicações **da Azure Ative Directory** > **Enterprise.**
3. Escolha o registo do microserviço OPC Vault `resourceGroupName-service`(por defeito, o seu).
4. Ir para **Utilizadores e Grupos**.
5. Selecione um utilizador com uma função a remover e, em seguida, **selecione Remover**.
6. Para utilizadores removidos na função 'Administrador' ou Aprovador, remova-os também das políticas do Cofre chave Azure.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Adicione a política de acesso ao utilizador ao Cofre de Chaves Azure

São necessárias políticas de acesso adicionais para aprovadores e administradores.

Por predefinição, a identidade do serviço tem apenas permissões limitadas para aceder ao Key Vault, para evitar que operações elevadas ou alterações ocorram sem a personificação do utilizador. As permissões básicas de serviço são Get and List, tanto para segredos como certificados. Para segredos, há apenas uma exceção: o serviço pode apagar uma chave privada da loja secreta depois de ser aceite por um utilizador. Todas as outras operações requerem permissões personificadas pelo utilizador.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Para uma função de Aprovador, as seguintes permissões devem ser adicionadas ao Cofre chave

1. Abra o portal do Azure.
2. Vá ao seu `resourceGroupName`Cofre OPC, usado durante a implantação.
3. Vai para o `resourceGroupName-xxxxx`Cofre da Chave.
4. Ir para as Políticas de **Acesso.**
5. **Selecione Adicionar novo**.
6. Ignore o modelo. Não há modelo que corresponda aos requisitos.
7. Escolha **Selecionar o Principal**, e selecione o utilizador a adicionar, ou convide um novo utilizador para o inquilino.
8. Selecione as **seguintes permissões chave**: **Obter,** **Lista,** e **Assinar**.
9. Selecione as **seguintes permissões secretas**: **Obter,** **Lista,** **Definir**e **Eliminar**.
10. Selecione as **seguintes permissões de Certificado**: **Obter** e **Lista**.
11. Selecione **OK**, e selecione **Guardar**.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Para um papel de Administrador, as seguintes permissões devem ser adicionadas ao Cofre chave

1. Abra o portal do Azure.
2. Vá ao seu `resourceGroupName`Cofre OPC, usado durante a implantação.
3. Vai para o `resourceGroupName-xxxxx`Cofre da Chave.
4. Ir para as Políticas de **Acesso.**
5. **Selecione Adicionar novo**.
6. Ignore o modelo. Não há modelo que corresponda aos requisitos.
7. Escolha **Selecionar o Principal**, e selecione o utilizador a adicionar, ou convide um novo utilizador para o inquilino.
8. Selecione as **seguintes permissões chave**: **Obter,** **Lista,** e **Assinar**.
9. Selecione as **seguintes permissões secretas**: **Obter,** **Lista,** **Definir**e **Eliminar**.
10. Selecione as **seguintes permissões de Certificado**: **Obter,** **Lista,** **Atualizar,** **Criar**e **Importar**.
11. Selecione **OK**, e selecione **Guardar**.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Remova a política de acesso ao utilizador do Cofre de Chaves Azure

1. Abra o portal do Azure.
2. Vá ao seu `resourceGroupName`Cofre OPC, usado durante a implantação.
3. Vai para o `resourceGroupName-xxxxx`Cofre da Chave.
4. Ir para as Políticas de **Acesso.**
5. Encontre o utilizador para remover e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir certificados e utilizadores do OPC Vault, pode:

> [!div class="nextstepaction"]
> [Comunicação segura de dispositivos OPC](howto-opc-vault-secure.md)
