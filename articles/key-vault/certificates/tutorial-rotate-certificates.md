---
title: Tutorial - Atualizar a frequência de rotação automática do certificado no Cofre chave [ Microsoft Docs
description: Tutorial mostrando como atualizar a frequência de rotação automática de um certificado no Cofre chave Azure usando o portal Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 2e6c250a0bcb9d73e7c572dfe8138c31269993e8
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106805"
---
# <a name="tutorial-configuring-certificates-auto-rotation-in-key-vault"></a>Tutorial: Configurar a rotação automática do certificado no Cofre chave

O Azure Key Vault permite-lhe fornecer, gerir e implementar certificados digitais facilmente. Podem ser certificados SSL/TLS públicos e privados assinados pela Autoridade de Certificados ou um certificado auto-assinado. A Key Vault também pode solicitar e renovar certificados através de parcerias com as autoridades de certificados, fornecendo uma solução robusta para a gestão do ciclo de vida do certificado. Neste tutorial, irá atualizar os atributos do certificado - período de validade, frequência de rotação automática, CA. Para obter mais informações sobre o Key Vault, reveja a [Descrição Geral](../general/overview.md).

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Gerir um certificado utilizando o portal Azure
> * Adicionar Conta de prestador de certificados
> * Período de validade do certificado de atualização
> * Frequência de rotação automática do certificado de atualização
> * Atualização dos atributos do certificado usando Azure Powershell


Antes de começar, leia [os conceitos básicos do Key Vault.](../general/basic-concepts.md) 

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vault"></a>Criar um cofre

Crie ou selecione o seu Cofre chave existente para realizar operações. [(Passos para criar um cofre chave).](../quick-create-portal.md) No exemplo, o nome Vault é **Exemplo-Vault**. 

![Resultado após a conclusão da criação do Key Vault](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Criar um certificado no Cofre chave

Crie ou importe um certificado no cofre. [(Passos para criar certificado no cofre chave).](../quick-create-portal.md) Neste caso, trabalhamos em certificado chamado **ExemploCertificado**.

> [!NOTE]
> No Azure Key Vault, os atributos do ciclo de vida de um certificado podem ser atualizados tanto no momento da criação do certificado como após a sua criação. 
## <a name="updating-certificates-life-cycle-attributes"></a>Atualizar os atributos do ciclo de vida do Certificado

Um certificado criado no Cofre chave pode ser 
- um certificado auto-assinado
- um certificado criado com uma Autoridade de Certificados (CA) que é parceiro com a Key Vault
- um certificado com uma Autoridade de Certificados que não é parceiro com key vault

As seguintes Autoridades de Certificados são atualmente fornecedores parceiros com a Key Vault:
- DigiCert - Key Vault oferece certificados OV TLS/SSL com DigiCert.
- GlobalSign - Key Vault oferece certificados OV TLS/SSL com GlobalSign.

Azure Key Vault roda certificados através de parcerias com as autoridades de certificados. Através dessa parceria estabelecida, a Key Vault solicita e renova automaticamente os certificados. Portanto, a **capacidade de rotação automática não é aplicável para certificados criados com CAs que não são parceiros com o Key Vault.** 

> [!NOTE]
> Um administrador de conta para um fornecedor de CA cria credenciais a serem usadas pela Key Vault para criar, renovar e utilizar certificados TLS/SSL via Key Vault.
![Autoridade de certificação](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
> 


### <a name="updating-certificates-life-cycle-attributes-at-the-time-of-certificate-creation"></a>Atualização dos atributos do ciclo de vida do Certificado no momento da criação de Certificados

1. Nas páginas de propriedades do Cofre-Chave, selecione **Certificados**.
2. Clique em **Gerar/Importar**.
3. Na **Atualização** do ecrã Criar um certificado os seguintes valores:
    

    - **Período de validade**: Insira o valor (em meses). A criação de certificados de curta duração é uma prática de segurança recomendada. Por predefinição, o valor de validade de um certificado recém-criado é de 12 meses.
    - **Tipo**de ação vitalícia : Selecione a ação de renovação automática e alerta do certificado. De acordo com a seleção, atualize a "percentagem de vida" ou o "Número de dias antes do termo". Por padrão, a renovação automática de um certificado é fixada em 80% da sua vida útil.<br> A partir do menu de descida, selecione a opção:

    |  Renovar automaticamente num dado momento| Envie um e-mail a todos os contactos num dado momento |
    |-----------|------|
    |A seleção desta opção irá ligar a rotação automática | Selecionando esta opção NÃO irá rodar automaticamente, só irá alertar os contactos|
        


4. Clique em **Criar**.

![Ciclo de vida do certificado](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="updating-life-cycle-attributes-of-stored-certificate"></a>Atualizar os atributos do ciclo de vida do certificado armazenado

1. Selecione o Cofre chave.
2. Nas páginas de propriedades do Cofre-Chave, selecione **Certificados**.
3. Selecione o certificado que pretende atualizar. Neste caso, trabalharemos em certificado chamado **ExemploCertificado**.
4. Selecione Política de **Emissão** a partir da barra de menu superior.

![Propriedades de certificado](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)
5. No ecrã política de **emissão,** atualize os seguintes valores:
- **Período de Validade**: Atualizar o valor (em meses)
- **Tipo**de ação vitalícia : Selecione a ação de renovação automática e alerta do certificado. De acordo com a seleção, atualize a "percentagem de vida" ou "Número de dias antes do termo". 

![Propriedades de certificado](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)
6. Clique em **Guardar**.

> [!IMPORTANT]
> A alteração do Tipo de Ação Vitalícia para um certificado registará imediatamente modificações para os certificados existentes.


### <a name="updating-certificates-attributes-using-powershell"></a>Atualizar os atributos do Certificado usando powerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Para modificar a política de renovação de uma lista de certificados, input File.csv contendo VaultName,CertName <br/>
>  cofre1,Cert1 <br/>
>  cofre2,Cert2
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Saiba mais sobre os parâmetros [aqui](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes)

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais do Key Vault têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não for necessário, elimine o grupo de recursos, que elimina o Key Vault e todos os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:**, escreva o nome do grupo de recursos e selecione **Eliminar**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, atualizou o ciclo de vida de um certificado. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

Ler mais sobre [gestão da criação de certificados no Cofre chave azure](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)
- Reveja a visão geral do [cofre de chaves](../general/overview.md)