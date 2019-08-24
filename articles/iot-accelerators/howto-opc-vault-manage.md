---
title: Como gerenciar o serviço de certificado do cofre OPC – Azure | Microsoft Docs
description: Gerencie os certificados de autoridade de certificação raiz do cofre OPC e as permissões de usuário.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 4420e5b0d895f8ea30dbd39fc50dd7480d57d086
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996000"
---
# <a name="how-to-manage-the-opc-vault-certificate-service"></a>Como gerenciar o serviço de certificado do cofre OPC

Este artigo explica as tarefas administrativas para o serviço de gerenciamento de certificados do cofre do OPC no Azure, como renovar certificados de autoridade de certificação do emissor, como renovar a CRL (lista de certificados revogados) e como conceder e revogar o acesso do usuário.

## <a name="create-or-renew-the-root-ca-certificate"></a>Criar ou renovar o certificado de autoridade de certificação raiz

Para criar o certificado de autoridade de certificação raiz é uma etapa obrigatória após a implantação. Sem um certificado de autoridade de certificação do emissor válido, não é possível assinar e emitir certificados de aplicativo.<br>Consulte o capítulo sobre os [tempos de vida do certificado](howto-opc-vault-secure-ca.md#certificates) para gerenciar seus certificados com tempos de vida seguros e razoáveis. Um certificado de autoridade de certificação do emissor deve ser renovado após metade de seu tempo de vida, mas não depois do tempo de vida configurado de um certificado de aplicativo assinado recentemente excederia o tempo de vida do certificado do emissor.<br>
> [!IMPORTANT]
> A função ' Administrador ' é necessária para criar ou renovar o certificado de autoridade de certificação do emissor.

1. Abra seu serviço de certificado `https://myResourceGroup-app.azurewebsites.net` em e entre.
2. Navegue até a `Certificate Groups` página.
3. Há um `Default` grupo de certificados listado. Clique em `Edit`.
4. No `Edit Certificate Group Details` , você pode modificar o nome da entidade e o tempo de vida de seus certificados de autoridade de certificação e de aplicativo.<br>O assunto e os tempos de vida devem ser definidos apenas uma vez antes que o primeiro certificado de autoridade de certificação seja emitido. As alterações de tempo de vida durante as operações podem resultar em tempos de vida inconsistentes de certificados e CRLs emitidos.
5. Insira um assunto válido, por exemplo, `CN=My CA Root, O=MyCompany, OU=MyDepartment`.<br>
   > [!IMPORTANT]
   > Alterar o assunto requer para renovar o certificado do emissor ou o serviço falhará ao assinar certificados de aplicativo. O assunto da configuração é a sanidade verificada em relação ao assunto do certificado do emissor ativo. Se as entidades não corresponderem, a assinatura de certificado será recusada.
6. Clique no `Save` botão.
7. Se você clicar em um erro "proibido" neste ponto, suas credenciais de usuário não terão a permissão de administrador para modificar ou criar um novo certificado raiz. Por padrão, o usuário que implantou o serviço tem funções de administrador e assinatura com o serviço, outros usuários precisam ser adicionados às funções ' aprovador ', ' gravador ' ou ' Administrador ', conforme apropriado no registro do aplicativo AzureAD.
8. Clique no `Details` botão. O `View Certificate Group Details` deve exibir as informações atualizadas.
9. Clique no `Renew CA Certificate` botão para emitir o primeiro certificado de autoridade de certificação do emissor ou para renovar o certificado do emissor. Pressione `Ok` para continuar.
10. Depois de alguns segundos, `Certificate Details` os são mostrados. Pressione `Issuer` ou`Crl` para baixar o certificado de autoridade de certificação e a CRL mais recentes para distribuição para seus aplicativos OPC UA.
11. Agora o serviço de gerenciamento de certificados do OPC UA está pronto para emitir certificados para aplicativos OPC UA.

## <a name="renew-the-crl"></a>Renovar a CRL

A renovação da CRL (lista de certificados revogados) é uma atualização, que deve ser distribuída para os aplicativos em intervalos regulares. Os dispositivos OPC UA, que dão suporte à extensão X509 do ponto de distribuição da CRL, podem atualizar diretamente a CRL do ponto de extremidade de microatendimento. Outros dispositivos OPC UA podem exigir atualizações manuais ou, na melhor das hipóteses, podem ser atualizados usando o GDS Server Push Extensions (*) para atualizar as listas de confiança com certificados e CRLs.

No fluxo de trabalho a seguir, todas as solicitações de certificado nos Estados excluídos são revogadas nas CRLs, que correspondem ao certificado de autoridade de certificação do emissor para o qual foram emitidas. O número de versão da CRL é incrementado em 1. <br>
> [!NOTE]
> Todas as CRLs emitidas são válidas até a expiração do certificado da AC do emissor, porque a especificação OPC UA não requer um modelo de distribuição determinístico obrigatório para a CRL.

> [!IMPORTANT]
> A função ' Administrador ' é necessária para renovar a CRL do emissor.

1. Abra seu serviço de certificado `https://myResourceGroup.azurewebsites.net` em e entre.
2. Navegue até a `Certificate Groups` página.
3. Clique no `Details` botão. O `View Certificate Group Details` deve exibir as informações atuais de certificado e CRL.
4. Clique no `Update CRL Revocation List(CRL)` botão para emitir uma CRL atualizada para todos os certificados de emissor ativos no armazenamento do cofre OPC.
5. Depois de alguns segundos, `Certificate Details` os são mostrados. Pressione `Issuer` ou`Crl` para baixar o certificado de autoridade de certificação e a CRL mais recentes para distribuição para seus aplicativos OPC UA.

## <a name="manage-user-roles"></a>Gerir funções de utilizador

As funções de usuário para o microserviço do compartimento OPC são gerenciadas no aplicativo Azure Active Directory Enterprise.

Para obter uma descrição detalhada das definições de função, consulte a seção [funções](howto-opc-vault-secure-ca.md#roles) .

Por padrão, um usuário autenticado no locatário pode entrar no serviço como um ' leitor '. Funções com privilégios mais altos exigem gerenciamento manual no portal do Azure ou usando o PowerShell.

### <a name="add-user"></a>Adicionar utilizador

1. Abra o portal do Azure em `portal.azure.com`.
2. Navegue até `Azure Active Directory`. / `Enterprise applications`
3. Escolha o registro do microserviço do cofre OPC, por padrão, `resourceGroupName-service`seu.
4. Navegue para `Users and Groups`.
5. Clique em `Add User`.
6. Selecione ou convide o usuário para atribuição a uma função específica.
7. Selecione a função para os usuários.
8. Pressione o `Assign` botão.
9. Para usuários no `Administrator` ou `Approver` na função, continue a adicionar políticas de acesso de Azure Key Vault.

### <a name="remove-user"></a>Remover utilizador

1. Abra o portal do Azure em `portal.azure.com`.
2. Navegue até `Azure Active Directory`. / `Enterprise applications`
3. Escolha o registro do microserviço do cofre OPC, por padrão, `resourceGroupName-service`seu.
4. Navegue para `Users and Groups`.
5. Selecione um usuário com uma função a ser removida.
6. Pressione o `Remove` botão.
7. Remova os administradores e aprovadores removidos também das políticas de Azure Key Vault.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Adicionar política de acesso de usuário a Azure Key Vault

Políticas de acesso adicionais são necessárias para Aprovadores e **Administradores**.

Por padrão, a identidade do serviço tem apenas permissões limitadas para acessar Key Vault para impedir que operações elevadas ou alterações ocorram sem a representação do usuário. As permissões básicas de serviço `Get` são `List` e para os segredos e certificados. Para segredos, há apenas uma exceção, o serviço pode ser `Delete` uma chave privada do repositório secreto depois de aceita por um usuário. Todas as outras operações exigem permissões representadas pelo usuário.<br>

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Para uma **função** de aprovador, as seguintes permissões devem ser adicionadas a Key Vault:

1. Abra o portal do Azure em `portal.azure.com`.
2. Navegue até o cofre `resourceGroupName`OPC usado durante a implantação.
3. Navegue até a Key Vault `resourceGroupName-xxxxx`.
4. Navegue até o `Access Policies`.
5. Clique em `Add new`.
6. Ignorar o modelo, não há nenhum modelo, que atenda aos requisitos.
7. Clique em `Select Principal` ativado e selecione o usuário a ser adicionado ou convide um novo usuário para o locatário.
8. Verifique `Key permissions` :`Get`e o mais importante `Sign`. `List`
9. Marque `Secret permissions`: `Get`, `List` e.`Delete` `Set`
10. Marque `Certificate permissions`: `Get`e .`List`
11. Clique em `Ok`.
12. `Save`for.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Para uma **função de administrador** , as seguintes permissões devem ser adicionadas a Key Vault:

1. Abra o portal do Azure em `portal.azure.com`.
2. Navegue até o cofre `resourceGroupName`OPC usado durante a implantação.
3. Navegue até a Key Vault `resourceGroupName-xxxxx`.
4. Navegue até o `Access Policies`.
5. Clique em `Add new`.
6. Ignorar o modelo, não há nenhum modelo, que atenda aos requisitos.
7. Clique em `Select Principal` ativado e selecione o usuário a ser adicionado ou convide um novo usuário para o locatário.
8. Verifique `Key permissions` :`Get`e o mais importante `Sign`. `List`
9. Marque `Secret permissions`: `Get`, `List` e.`Delete` `Set`
10. Marque `Certificate permissions`: `Get`, `List`, `Update`e. `Create` `Import`
11. Clique em `Ok`.
12. `Save`for.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Remover política de acesso do usuário da Azure Key Vault

1. Abra o portal do Azure em `portal.azure.com`.
2. Navegue até o cofre `resourceGroupName`OPC usado durante a implantação.
3. Navegue até a Key Vault `resourceGroupName-xxxxx`.
4. Navegue até o `Access Policies`.
5. Localize o usuário a ser removido e clique `... / Delete` em para excluir o acesso do usuário.

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a gerenciar os certificados e usuários do cofre OPC, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Comunicação segura de dispositivos OPC](howto-opc-vault-secure.md)
