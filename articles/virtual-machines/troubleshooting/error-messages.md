---
title: Códigos de erro comuns da VM no Azure | Microsoft Docs
description: Entenda alguns dos códigos de erro comuns encontrados ao provisionar e gerenciar máquinas virtuais no Azure
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: f5639d1cf94c77d699dc6de9841698b045ac1f96
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543023"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Entender mensagens de erro comuns ao gerenciar máquinas virtuais no Azure

Este artigo descreve alguns dos códigos de erro e mensagens mais comuns que você pode encontrar ao criar ou gerenciar VMs (máquinas virtuais) no Azure.

>[!NOTE]
> Você pode deixar comentários nesta página para obter comentários ou por meio de [comentários do Azure](https://feedback.azure.com/forums/216843-virtual-machines) com #azerrormessage marca.

## <a name="error-response-format"></a>Formato de resposta de erro 
As VMs do Azure usam o seguinte formato JSON para resposta de erro:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner level error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Uma resposta de erro sempre inclui um código de status e um objeto de erro. Cada objeto de erro sempre contém um código de erro e uma mensagem. Se a VM for criada com um modelo, o objeto de erro também conterá uma seção de detalhes que contém um nível interno de códigos de erro e mensagem. Normalmente, o nível mais interno da mensagem de erro é a falha raiz.


## <a name="common-virtual-machine-management-errors"></a>Erros comuns de gerenciamento de máquinas virtuais

Esta seção lista as mensagens de erro comuns que você pode encontrar ao gerenciar VMs:

|  Código de Erro  |  Mensagem de Erro  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Falha ao adquirir concessão ao criar o disco '{0}' usando blob com URI {1}. O blob já está em uso.  |  
|  AllocationFailed  |  Falha na alocação. Tente reduzir o tamanho da VM ou o número de VMs, tente novamente mais tarde ou tente implantar em um conjunto de disponibilidade diferente ou em um local diferente do Azure.  |  
|  AllocationFailed  |  Falha na alocação de VM devido a um erro interno. Tente novamente mais tarde ou tente implantar em um local diferente.  |
|  ArtifactNotFound  |  Não foi possível encontrar a extensão de VM com o Publicador '{0}' e o tipo '{1}' no local '{2}'.  |
|  ArtifactNotFound  |  Não foi possível encontrar a extensão com o Publicador '{0}', tipo '{1}' e versão do manipulador de tipo '{2}' no repositório de extensões.  |
|  ArtifactVersionNotFound  |  Nenhuma versão encontrada no repositório de artefatos que satisfaça a versão solicitada '{0}'.  |
|  ArtifactVersionNotFound  |  Nenhuma versão encontrada no repositório de artefatos que satisfaça a versão solicitada '{0}' para a extensão de VM com o Publicador '{1}' e tipo '{2}'.  |
|  AttachDiskWhileBeingDetached  |  Não é possível anexar o disco de dados '{0}' à VM '{1}' porque o disco está sendo desanexado no momento. Aguarde até que o disco seja completamente desanexado e tente novamente.  |
|  BadRequest  |  Ainda não há suporte para os conjuntos de disponibilidade alinhados nesta região.  |
|  BadRequest  |  Não há suporte para a adição de uma VM com discos gerenciados a um conjunto de disponibilidade não gerenciado ou para a adição de uma VM com discos baseados em blob para o conjunto de disponibilidade gerenciado. Crie um conjunto de disponibilidade com a propriedade ' Managed ' definida para adicionar uma VM com discos gerenciados a ele.  |
|  BadRequest  |  Não há suporte para Managed Disks nesta região.  |
|  BadRequest  |  Não há suporte para vários VMExtensions por manipulador para o tipo de sistema operacional '{0}'. VMExtension '{1}' com o manipulador '{2}' já foi adicionado ou especificado na entrada.  |
|  BadRequest  |  Não há suporte para a operação '{0}' no recurso '{1}' com discos gerenciados.  |
|  CertificateImproperlyFormatted  |  A representação JSON do segredo recuperada de {0} tem um campo de dados que não é um arquivo PFX formatado corretamente ou a senha fornecida não decodifica o arquivo PFX corretamente.  |
|  CertificateImproperlyFormatted  |  Os dados recuperados do {0} não são desserializáveis em JSON.  |
|  Conflito  |  O redimensionamento de disco é permitido somente ao criar uma VM ou quando a VM é desalocada.  |
|  ConflictingUserInput  |  Não é possível anexar o disco '{0}', pois o disco já pertence à VM '{1}'.  |
|  ConflictingUserInput  |  Os grupos de recursos de origem e de destino são os mesmos.  |
|  ConflictingUserInput  |  As contas de armazenamento de origem e de destino para {0} de disco são diferentes.  |
|  ContainerAlreadyOnLease  |  Já existe uma concessão no contêiner de armazenamento que contém o blob com URI {0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  A solicitação mover recursos contém recursos do keyvault que são referenciados por um ou mais {0}s na solicitação. Não há suporte para isso no momento na movimentação entre assinaturas. Verifique os detalhes do erro para as IDs de recurso do keyvault.  |
|  DiagnosticsOperationInternalError  |  Ocorreu um erro interno ao processar o perfil de diagnóstico do {0}da VM.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  O blob {0} já está em uso por outro disco que pertence à VM '{1}'. Você pode examinar os metadados de BLOB para as informações de referência de disco.  |
|  DiskBlobNotFound  |  Não é possível localizar blob VHD com URI {0} para o disco '{1}'.  |
|  DiskBlobNotFound  |  Não é possível localizar o blob VHD com o URI {0}.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} segredo não tem as marcas de {1}. Atualize a versão secreta, adicione as marcas necessárias e tente novamente.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Falha ao desencapsular o valor de {0} secreto usando {1} de chave.  |
|  DiskImageNotReady  |  A {0} de imagem do disco está em estado de {1}. Tente novamente quando a imagem estiver pronta.  |
|  DiskPreparationError  |  Um ou mais erros ocorreram ao preparar discos de VM. Consulte exibição da instância de disco para obter detalhes.  |
|  DiskProcessingError  |  O processamento do disco foi interrompido porque a VM tem outros discos em discos com falha.  |
|  ImageBlobNotFound  |  Não é possível localizar blob VHD com URI {0} para o disco '{1}'.  |
|  ImageBlobNotFound  |  Não é possível localizar o blob VHD com o URI {0}.  |
|  IncorrectDiskBlobType  |  Os blobs de disco só podem ser do tipo blob de páginas. O {0} de BLOB para o disco '{1}' é do tipo blob de blocos.  |
|  IncorrectDiskBlobType  |  Os blobs de disco só podem ser do tipo blob de páginas. O {0} de blob é do tipo '{1}'.  |
|  IncorrectImageBlobType  |  Os blobs de disco só podem ser do tipo blob de páginas. O {0} de BLOB para o disco '{1}' é do tipo blob de blocos.  |
|  IncorrectImageBlobType  |  Os blobs de disco só podem ser do tipo blob de páginas. O {0} de blob é do tipo '{1}'.  |
|  InternalOperationError  |  Não foi possível resolver a {0}da conta de armazenamento. Verifique se ele foi criado por meio do provedor de recursos de armazenamento no mesmo local que o recurso de computação.  |
|  InternalOperationError  |  Falha ao {0} tarefas de busca de meta.  |
|  InternalOperationError  |  Erro ao validar o perfil de rede da VM '{0}'.  |
|  InvalidAccountType  |  O {0} de AccountType é inválido.  |
|  InvalidParameter  |  O valor do parâmetro {0} é inválido.  |
|  InvalidParameter  |  A senha de administrador especificada não é permitida.  |
|  InvalidParameter  |  "A senha fornecida deve ter entre {0}caracteres de{1} e deve atender pelo menos {2} de requisitos de complexidade de senha do seguinte: <ol><li> Contém um caractere maiúsculo</li><li>Contém um caractere minúsculo</li><li>Contém um dígito numérico</li><li>Contém um caractere especial.</li></ol>  |
|  InvalidParameter  |  O nome de usuário do administrador especificado não é permitido.  |
|  InvalidParameter  |  Não será possível anexar um disco de sistema operacional existente se a VM for criada a partir de uma imagem de plataforma ou de usuário.  |
|  InvalidParameter  |  O nome do contêiner {0} é inválido. Os nomes de contêiner devem ter de 3-63 caracteres de comprimento e podem conter apenas caracteres alfanuméricos minúsculos e hifens. Hífen deve ser precedido e seguido por um caractere alfanumérico.  |
|  InvalidParameter  |  O nome do contêiner {0} na URL {1} é inválido. Os nomes de contêiner devem ter de 3-63 caracteres de comprimento e podem conter apenas caracteres alfanuméricos minúsculos e hifens. Hífen deve ser precedido e seguido por um caractere alfanumérico.  |
|  InvalidParameter  |  O nome do blob na URL {0} contém uma barra. Atualmente, não há suporte para isso em discos.  |
|  InvalidParameter  |  O URI {0} não parece ser um URI de blob correto.  |
|  InvalidParameter  |  Um disco chamado '{0}' já usa o mesmo LUN: {1}.  |
|  InvalidParameter  |  Já existe um disco chamado '{0}'.  |
|  InvalidParameter  |  Não é possível especificar substituições de imagem de usuário para um disco já definido na referência de imagem especificada.  |
|  InvalidParameter  |  Um disco chamado '{0}' já usa a mesma URL do VHD {1}.  |
|  InvalidParameter  |  A contagem de domínios de falha especificada {0} deve estar no intervalo de {1} a {2}.  |
|  InvalidParameter  |  O tipo de licença {0} é inválido. Os tipos de licença válidos são: Windows_Client ou Windows_Server, diferencia maiúsculas de minúsculas.  |
|  InvalidParameter  |  O nome do host do Linux não pode exceder {0} caracteres de comprimento ou conter os seguintes caracteres: {1}.  |
|  InvalidParameter  |  O caminho de destino para chaves públicas SSH está limitado no momento ao seu valor padrão {0} devido a um problema conhecido no agente de provisionamento do Linux.  |
|  InvalidParameter  |  Já existe um disco no LUN {0}.  |
|  InvalidParameter  |  A {0} de assinatura da solicitação deve corresponder à assinatura {1} contida na ID do disco gerenciado.  |
|  InvalidParameter  |  Os dados personalizados em OSProfile devem estar na codificação Base64 e com um comprimento máximo de {0} caracteres.  |
|  InvalidParameter  |  O nome do blob na URL {0} deve terminar com a extensão '{1}'.  |
|  InvalidParameter  |  {0}' não é um prefixo de nome de blob VHD capturado válido. Um prefixo válido corresponde ao Regex '{1}'.  |
|  InvalidParameter  |  Não será possível adicionar certificados à sua VM se o agente de VM não estiver provisionado.  |
|  InvalidParameter  |  Já existe um disco no LUN {0}.  |
|  InvalidParameter  |  Não é possível criar a VM porque o tamanho solicitado {0} não está disponível no cluster em que o conjunto de disponibilidade está alocado no momento. Os tamanhos disponíveis são: {1}. Leia mais sobre a estratégia de redimensionamento de VM em https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  O tamanho da VM solicitado {0} não está disponível na região atual. Os tamanhos disponíveis na região atual são: {1}. Saiba mais sobre os tamanhos de VM disponíveis em cada região em https://aka.ms/azure-regions.  |
|  InvalidParameter  |  O tamanho da VM solicitado {0} não está disponível na região atual. Saiba mais sobre os tamanhos de VM disponíveis em cada região em https://aka.ms/azure-regions.  |
|  InvalidParameter  |  O nome de usuário do administrador do Windows não pode ter mais de {0} caracteres de comprimento, terminar com um ponto (.) ou conter os seguintes caracteres: {1}.  |
|  InvalidParameter  |  O nome do computador Windows não pode ter mais de {0} caracteres, ser totalmente numéricos ou conter os seguintes caracteres: {1}.  |
|  MissingMoveDependentResources  |  A solicitação mover recursos não contém todos os recursos dependentes. Verifique os detalhes do erro para obter as IDs de recurso ausentes.  |
|  MoveResourcesHaveInvalidState  |  A solicitação mover recursos contém VMs que estão associadas a contas de armazenamento inválidas. Verifique os detalhes dessas IDs de recurso e os nomes de conta de armazenamento referenciados.  |
|  MoveResourcesHavePendingOperations  |  A solicitação mover recursos contém recursos para os quais uma operação está pendente. Verifique os detalhes dessas IDs de recurso. Repita a operação depois que as operações pendentes forem concluídas.  |
|  MoveResourcesNotFound  |  A solicitação mover recursos contém recursos que não podem ser encontrados. Verifique os detalhes dessas IDs de recurso.  |
|  NetworkingInternalOperationError  |  Erro de alocação de rede desconhecido.  |
|  NetworkingInternalOperationError  |  Erro de alocação de rede desconhecido  |
|  NetworkingInternalOperationError  |  Ocorreu um erro interno ao processar o perfil de rede da VM.  |
|  NotFound  |  Não é possível encontrar o {0} do conjunto de disponibilidade.  |
|  NotFound  |  A máquina virtual de origem '{0}' especificada na solicitação não existe neste local do Azure.  |
|  NotFound  |  Locatário com ID {0} não encontrado.  |
|  NotFound  |  Não é possível encontrar a {0} de imagem.  |
|  Sem suporte  |  O tipo de licença é {0}, mas o blob de imagem {1} não é local.  |
|  OperationNotAllowed  |  O conjunto de disponibilidade {0} não pode ser excluído. Antes de excluir um conjunto de disponibilidade, verifique se ele não contém nenhuma VM.  |
|  OperationNotAllowed  |  Não é permitido alterar o SKU do conjunto de disponibilidade de ' aligned ' para ' Classic '.  |
|  OperationNotAllowed  |  Não é possível modificar extensões na VM quando a VM não está em execução.  |
|  OperationNotAllowed  |  A ação de captura só tem suporte em uma máquina virtual com discos baseados em BLOB. Use as APIs de recurso de ' imagem ' para criar uma imagem de uma máquina virtual gerenciada.  |
|  OperationNotAllowed  |  O {0} de recursos não pode ser criado a partir de {1} de imagem até que a imagem tenha sido criada com êxito.  |
|  OperationNotAllowed  |  As atualizações para encryptionSettings não são permitidas quando a VM é alocada, tente novamente depois que a VM for desalocada  |
|  OperationNotAllowed  |  Não há suporte para a adição de um disco gerenciado a uma VM com discos baseados em BLOB.  |
|  OperationNotAllowed  |  O número máximo de discos de dados que podem ser anexados a uma VM desse tamanho é {0}.  |
|  OperationNotAllowed  |  Não há suporte para a adição de um disco baseado em blob para VM com discos gerenciados.  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida na imagem '{1}', pois a imagem está marcada para exclusão. Você só pode repetir a operação de exclusão (ou aguardar uma conclusão em andamento).  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida na VM '{1}' porque a VM está generalizada.  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida, pois a coleção de pontos de restauração '{1}' está marcada para exclusão.  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida na extensão de VM '{1}', pois está marcada para exclusão. Você só pode repetir a operação de exclusão (ou aguardar uma conclusão em andamento).  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida porque as máquinas virtuais '{1}' estão sendo provisionadas usando a imagem '{2}'.  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida porque o dimensionamento de máquina virtual '{1}' está usando a imagem '{2}' no momento.  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida na VM '{1}', pois a VM está marcada para exclusão. Você só pode repetir a operação de exclusão (ou aguardar uma conclusão em andamento).  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida na VM '{1}', uma vez que a VM é desalocada ou marcada para ser desalocada.  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida na VM '{1}' porque a VM está em execução. Desligue explicitamente, caso você desligue a VM de dentro do sistema operacional convidado.  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida na VM '{1}', pois a VM não está desalocada.  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida na VM '{1}', pois a VM tem a extensão '{2}' no estado de falha.  |
|  OperationNotAllowed  |  A operação '{0}' não é permitida na VM '{1}' porque outra operação está em andamento.  |
|  OperationNotAllowed  |  A operação '{0}' requer que a máquina virtual '{1}' seja generalizada.  |
|  OperationNotAllowed  |  A operação requer que a VM esteja em execução (ou definida para execução).  |
|  OperationNotAllowed  |  O disco com tamanho {0}GB, que é menor do que o tamanho {1}GB do disco correspondente na imagem, não é permitido.  |
|  OperationNotAllowed  |  As extensões do conjunto de dimensionamento de VMs do manipulador '{0}' podem ser adicionadas somente no momento da criação do conjunto de dimensionamento de VM.  |
|  OperationNotAllowed  |  As extensões do conjunto de dimensionamento de VMs do manipulador '{0}' só podem ser excluídas no momento da exclusão do conjunto de escala de VM.  |
|  OperationNotAllowed  |  A VM '{0}' já está usando discos gerenciados.  |
|  OperationNotAllowed  |  A VM '{0}' pertence ao conjunto de disponibilidade ' clássico ' '{1}'. Atualize o conjunto de disponibilidade para usar o SKU ' alinhado ' e tente a conversão novamente.  |
|  OperationNotAllowed  |  A VM criada a partir da imagem não pode ter discos baseados em BLOB. Todos os discos precisam ser discos gerenciados.  |
|  OperationNotAllowed  |  A operação de captura não pode ser concluída porque a VM não está generalizada.  |
|  OperationNotAllowed  |  As operações de gerenciamento na VM '{0}' não são permitidas porque os discos de VM estão sendo convertidos em discos gerenciados.  |
|  OperationNotAllowed  |  Uma operação em andamento está alterando o estado de energia da máquina virtual {0} para {1}. Execute a operação {2} após algum tempo.  |
|  OperationNotAllowed  |  Não é possível adicionar ou atualizar a VM. O tamanho da VM solicitado {0} pode não estar disponível na unidade de alocação existente. Leia mais sobre a estratégia de redimensionamento de VM em https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Não é possível redimensionar a VM porque o tamanho solicitado {0} não está disponível no cluster em que o conjunto de disponibilidade está alocado no momento. Os tamanhos disponíveis são: {1}. Leia mais sobre a estratégia de redimensionamento de VM em https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Não é possível redimensionar a VM porque o tamanho solicitado {0} não está disponível no cluster no qual a VM está alocada no momento. Para redimensionar sua VM para {1} Desaloque-a (esta é a operação de parada no portal do Azure) e tente a operação de redimensionamento novamente. Leia mais sobre a estratégia de redimensionamento de VM em https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Falha no provisionamento do sistema operacional para a VM '{0}' porque o sistema operacional convidado está sendo provisionado no momento.  |
|  OSProvisioningClientError  |  Falha no provisionamento do sistema operacional para a VM '{0}'. Detalhes do erro: {1} Verifique se a imagem foi preparada corretamente (generalizada). <ul><li>Instruções para Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Falha na geração da chave do host SSH. Detalhes do erro: {0}. Para resolver esse problema, verifique se o agente do Linux está configurado corretamente. <ul><li>Você pode verificar as instruções em: https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  O nome de usuário especificado para a VM é inválido para esta distribuição do Linux. Detalhes do erro: {0}.  |
|  OSProvisioningInternalError  |  Falha no provisionamento do sistema operacional para a VM '{0}' devido a um erro interno.  |
|  OSProvisioningTimedOut  |  O provisionamento do sistema operacional para a VM '{0}' não foi concluído no tempo alocado. A VM ainda pode concluir o provisionamento com êxito. Verifique o estado de provisionamento mais tarde.  |
|  OSProvisioningTimedOut  |  O provisionamento do sistema operacional para a VM '{0}' não foi concluído no tempo alocado. A VM ainda pode concluir o provisionamento com êxito. Verifique o estado de provisionamento mais tarde. Além disso, verifique se a imagem foi preparada corretamente (generalizada).   <ul><li>Instruções para Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruções para Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  O provisionamento do sistema operacional para a VM '{0}' não foi concluído no tempo alocado. No entanto, o agente convidado da VM foi detectado em execução. Isso sugere que o sistema operacional convidado não foi adequadamente preparado para ser usado como uma imagem de VM (com createoption = FromImage). Para resolver esse problema, use o VHD como está com createoption = Attach ou prepare-o corretamente para uso como uma imagem:   <ul><li>Instruções para Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruções para Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  O tamanho de VM necessário não está disponível no local selecionado.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  O recurso não pode ser atualizado no momento devido à atualização de plataforma em andamento. Tente novamente mais tarde.  |
|  StorageAccountLimitation  |  A conta de armazenamento '{0}' não oferece suporte a blobs de página que são necessários para criar discos.  |
|  StorageAccountLimitation  |  A conta de armazenamento '{0}' excedeu sua cota alocada.  |
|  StorageAccountLocationMismatch  |  Não foi possível resolver a {0}da conta de armazenamento. Verifique se ele foi criado por meio do provedor de recursos de armazenamento no mesmo local que o recurso de computação.  |
|  StorageAccountNotFound  |  A conta de armazenamento não {0} encontrada. Certifique-se de que a conta de armazenamento não seja excluída e pertença ao mesmo local do Azure que a VM.  |
|  StorageAccountNotRecognized  |  Use uma conta de armazenamento gerenciada pelo provedor de recursos de armazenamento. Não há suporte para o uso de {0}.  |
|  StorageAccountOperationInternalError  |  Ocorreu um erro interno ao acessar a {0}da conta de armazenamento.  |
|  StorageAccountSubscriptionMismatch  |  A {0} da conta de armazenamento não pertence a {1}de assinatura.  |
|  StorageAccountTooBusy  |  A conta de armazenamento '{0}' está muito ocupada atualmente. Considere usar outra conta.  |
|  StorageAccountTypeNotSupported  |  O {0} de disco usa {1} que é uma conta de armazenamento de BLOBs. Tente novamente com a conta de armazenamento de uso geral.  |
|  StorageAccountTypeNotSupported  |  A {0} da conta de armazenamento é do tipo {1}. O diagnóstico de inicialização dá suporte a {2} tipos de conta de armazenamento.  <ul><li>Esse erro ocorrerá se você usar a conta de armazenamento Premium para o diagnóstico de inicialização. Para obter mais informações, consulte [como usar o diagnóstico de inicialização](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  A assinatura não está autorizada.  |
|  TargetDiskBlobAlreadyExists  |  O blob {0} já existe. Forneça um URI de blob diferente para criar um novo disco de dados em branco '{1}'.  |
|  TargetDiskBlobAlreadyExists  |  A operação de captura não pode continuar porque o blob de imagem de destino já existe {0} e o sinalizador para substituir BLOBs VHD não está definido. Exclua o BLOB ou defina o sinalizador para substituir os BLOBs VHD e tente novamente.  |
|  TargetDiskBlobAlreadyExists  |  A operação de captura não pode continuar porque o blob da imagem de destino {0} tem uma concessão ativa.   |
|  TargetDiskBlobAlreadyExists  |  O blob {0} já existe. Forneça um URI de blob diferente como destino para o disco '{1}'.  |
|  TooManyVMRedeploymentRequests  |  Muitas solicitações de reimplantação foram recebidas para a VM '{0}' ou as VMs no mesmo availabilityset com esta VM. Tente novamente mais tarde.  |
|  VHDSizeInvalid  |  O valor de tamanho de disco especificado de {0} para o disco '{1}' com {2} de blob é inválido. O tamanho do disco deve estar entre {3} e {4}.  |
|  VMAgentStatusCommunicationError  |  A VM '{0}' não relatou o status do agente ou das extensões da VM. Verifique se a VM tem um agente de VM em execução e se pode estabelecer conexões de saída com o armazenamento do Azure.  |
|  VMArtifactRepositoryInternalError  |  Ocorreu um erro ao se comunicar com o repositório de artefatos para recuperar detalhes do artefato da VM.  |
|  VMArtifactRepositoryInternalError  |  Ocorreu um erro interno ao recuperar os dados de artefato da VM do repositório de artefatos.  |
|  VMExtensionHandlerNonTransientError  |  O manipulador '{0}' relatou uma falha para a extensão de VM '{1}' com o código de erro de terminal '{2}' e a mensagem de erro: '{3}'  |
|  VMExtensionManagementInternalError  |  Ocorreu um erro interno ao processar a extensão de VM '{0}'.  |
|  VMExtensionManagementInternalError  |  Ocorreram vários erros ao preparar as extensões de VM. Consulte exibição da instância de extensão da VM para obter detalhes.  |
|  VMExtensionProvisioningError  |  A VM relatou uma falha ao processar a extensão '{0}'. Mensagem de erro: "{1}".  |
|  VMExtensionProvisioningError  |  Falha ao provisionar várias extensões de VM na VM. Consulte a exibição da instância da extensão da VM para obter detalhes.  |
|  VMExtensionProvisioningTimeout  |  O provisionamento da extensão de VM '{0}' atingiu o tempo limite. A instalação da extensão pode estar demorando muito ou não foi possível obter o status da extensão.  |
|  VMMarketplaceInvalidInput  |  A criação de uma máquina virtual de uma imagem não do Marketplace não precisa de informações de plano. Remova as informações do plano na solicitação. O nome do disco do sistema operacional é {0}.  |
|  VMMarketplaceInvalidInput  |  As informações de compra não correspondem. Não é possível implantar a partir da imagem do Marketplace. O nome do disco do sistema operacional é {0}.  |
|  VMMarketplaceInvalidInput  |  A criação de uma máquina virtual da imagem do Marketplace requer informações de plano na solicitação. O nome do disco do sistema operacional é {0}.  |
|  VMNotFound  |  Não é possível encontrar a VM '{0}'.  |
|  VMRedeploymentFailed  |  Falha na reimplantação da VM '{0}' devido a um erro interno. Tente novamente mais tarde.  |
|  VMRedeploymentTimedOut  |  A reimplantação da VM '{0}' não foi concluída no tempo alocado. Ele pode ser concluído com êxito em algum momento. Caso contrário, você pode repetir a solicitação.  |
|  VMStartTimedOut  |  A VM '{0}' não foi iniciada no tempo alocado. A VM ainda pode ser iniciada com êxito. Verifique o estado de energia mais tarde.  |


## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, você poderá entrar em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.
