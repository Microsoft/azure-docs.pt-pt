---
title: Dados de encriptação de Inativos no Azure Stack
description: Saiba como a pilha do Azure protege os dados com a encriptação inativa
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 03/11/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
keywords: ''
ms.openlocfilehash: 018b8f6cf4fc5d3cd380535fca71a038b7fd4208
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769392"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>Dados de encriptação de Inativos no Azure Stack

O Azure Stack protege os dados de infraestrutura ao nível do subsistema de armazenamento com a encriptação em repouso e de utilizador. Subsistema de armazenamento do Azure Stack está encriptado com BitLocker com encriptação AES de 128 bits. Chaves do BitLocker são mantidas num arquivo de segredos interno.

Dados de encriptação de Inativos são um requisito comum para muitos dos padrões de conformidade principais (por exemplo, PCI-DSS, FedRAMP, HIPAA). O Azure Stack lhe permite satisfazer essas necessidades sem trabalho extra ou as configurações necessárias. Para obter mais informações sobre como o Azure Stack ajuda-o a cumprir as normas de conformidade, consulte a [Portal de confiança do serviço Microsoft](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> Dados de encriptação de Inativos protege os seus dados de que está sendo acessado por alguém que roubou a um ou mais discos rígidos fisicamente. Dados de encriptação de Inativos não protege contra dados a ser intercetados através da rede (dados em trânsito), dados atualmente a ser utilizados (dados na memória) ou mais em geral, os dados que está a ser exfiltrated enquanto o sistema está a funcionar e em execução.

## <a name="retrieving-bitlocker-recovery-keys"></a>Obter chaves de recuperação do BitLocker

Chaves de BitLocker de pilha do Azure para dados Inativos são gerenciadas internamente. Não tem de fornecê-los para operações normais ou durante o arranque do sistema. No entanto, os cenários de suporte podem exigir chaves de recuperação do BitLocker para o sistema online.  

> [!WARNING]
> Obter as chaves de recuperação do BitLocker e armazená-las numa localização segura fora do Azure Stack. Não ter as chaves de recuperação durante determinados cenários de suporte poderá resultar na perda de dados e requerem uma restauração do sistema de uma imagem de cópia de segurança.

Obter as chaves de recuperação do BitLocker precisa de acesso para o [ponto final com privilégios](azure-stack-privileged-endpoint.md) (PEP). A partir de uma sessão PEP, execute o cmdlet Get-AzsRecoveryKeys.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

Parâmetros opcionais para *Get-AzsRecoveryKeys* cmdlet:

| Parâmetro | Descrição | Type | Necessário |
|---------|---------|---------|---------|
|*raw* | devolve dados não processados do mapeamento entre a chave de recuperação, o nome de computador e IDs de palavra-passe de cada volume criptografado  | Comutador | Nenhum (Designed para cenários de suporte)|


## <a name="troubleshoot-issues"></a>Resolver problemas

Em circunstâncias extremas, desbloquear um BitLocker pedido pode falhar, resultando num volume específico para não efetuar o arranque. Dependendo da disponibilidade de alguns dos componentes da arquitetura, esta falha pode resultar em tempo de inatividade e a potencial perda de dados se não tiver as chaves de recuperação do BitLocker.

> [!WARNING]
> Obter as chaves de recuperação do BitLocker e armazená-las numa localização segura fora do Azure Stack. Não ter as chaves de recuperação durante determinados cenários de suporte poderá resultar na perda de dados e requerem uma restauração do sistema de uma imagem de cópia de segurança.

Se suspeitar de que o seu sistema está a ter problemas com o BitLocker, como o Azure Stack a conseguir iniciar, contacte o suporte. O suporte requer as chaves de recuperação do BitLocker. A maioria do BitLocker relacionada com problemas podem ser resolvidos com uma operação de FRU para essa VM/anfitrião/volume específico. Para os outros casos, pode ser efetuado um procedimento de desbloqueio manual usando chaves de recuperação do BitLocker. Se as chaves de recuperação do BitLocker não estiverem disponíveis, é a única opção restaurar a partir de uma imagem de backup. Dependendo de quando foi efetuada a última cópia de segurança, pode encontrar a perda de dados.

## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais sobre a segurança do Azure Stack](azure-stack-security-foundations.md)
- Para obter mais informações sobre como o BitLocker protege os CSVs, consulte [proteger o cluster shared volumes e redes de armazenamento com o BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).