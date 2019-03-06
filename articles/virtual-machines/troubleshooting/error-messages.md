---
title: Códigos de erro VM comuns no Azure | Documentos da Microsoft
description: Compreender alguns dos códigos de erro comuns encontrados durante a aprovisionar e gerir máquinas virtuais no Azure
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: 5945be210812a6cbc24c9a3bb12414be5212be17
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437603"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Compreender as mensagens de erro comuns na gestão de máquinas virtuais no Azure

Este artigo descreve alguns dos códigos de erro mais comuns e as mensagens que poderá encontrar ao criar ou gerir máquinas virtuais (VMs) no Azure.

>[!NOTE]
> Pode deixar comentários nesta página para comentários ou através de [comentários do Azure](https://feedback.azure.com/forums/216843-virtual-machines) com #azerrormessage etiqueta.

## <a name="error-response-format"></a>Formato de resposta de erro 
VMs do Azure utilizam o seguinte formato JSON para resposta de erro:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner evel error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Uma resposta de erro inclui sempre um código de estado e um objeto de erro. Cada objeto de erro contém sempre um código de erro e uma mensagem. Se a VM é criada com um modelo, o objeto de erro também contém uma seção de detalhes que contém um nível interno de códigos de erro e mensagem. Normalmente, o nível mais interno de mensagem de erro é a falha de raiz.


## <a name="common-virtual-machine-management-errors"></a>Erros comuns de gestão de máquina virtual

Esta secção lista as mensagens de erro comuns que poderá encontrar ao gerir VMs:

|  Código de Erro  |  Mensagem de Erro  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Falha ao adquirir concessão ao criar o disco '{0}' com o URI de blob {1}. BLOB já está em utilização.  |  
|  AllocationFailed  |  Falha na atribuição. Tente reduzir o tamanho da VM ou o número de VMs, tente novamente mais tarde ou experimente implementar um conjunto de disponibilidade diferente ou a localização do Azure diferente.  |  
|  AllocationFailed  |  A alocação da VM falhou devido a um erro interno. . Tente novamente mais tarde ou experimente implementar numa localização diferente.  |
|  ArtifactNotFound  |  A extensão VM com o publicador '{0}'e o tipo'{1}'não foi possível encontrar a localização'{2}'.  |
|  ArtifactNotFound  |  Extensão com o publicador '{0}', tipo '{1}"e a versão do tipo de manipulador '{2}' não foi possível encontrar o repositório de extensão.  |
|  ArtifactVersionNotFound  |  Foi encontrada nenhuma versão no repositório de artefactos que coincida com a versão pedida "{0}'.  |
|  ArtifactVersionNotFound  |  Foi encontrada nenhuma versão no repositório de artefactos que coincida com a versão pedida "{0}'para a extensão VM com o publicador'{1}'e o tipo'{2}'.  |
|  AttachDiskWhileBeingDetached  |  Não é possível anexar o disco de dados '{0}'para a VM"{1}' porque o disco está atualmente a ser desligado. Aguarde até que o disco tenha sido completamente desanexado e, em seguida, tente novamente.  |
|  BadRequest  |  Alinhado "conjuntos de disponibilidade ainda não são suportados nesta região.  |
|  BadRequest  |  Não é suportada a adição de uma VM com discos geridos para o conjunto de disponibilidade não gerido ou a adição de uma VM com discos baseados em blob para o conjunto de disponibilidade gerido. Crie um conjunto de disponibilidade com a propriedade "geridos" definida para adicionar uma VM com discos geridos ao mesmo.  |
|  BadRequest  |  Os Discos Geridos não são suportados nesta região.  |
|  BadRequest  |  Múltiplas VMExtensions por processador não suportada para o tipo de SO "{0}'. VMExtension "{1}'com o manipulador'{2}' já foi adicionada ou especificada na entrada.  |
|  BadRequest  |  Operação "{0}"não é suportada no recurso"{1}" com discos geridos.  |
|  CertificateImproperlyFormatted  |  Representação JSON do segredo obtida {0} tem um campo de dados que não é um ficheiro PFX corretamente formatado ou a palavra-passe fornecida não descodificar o ficheiro PFX corretamente.  |
|  CertificateImproperlyFormatted  |  Os dados recuperados da {0} não é são desserializáveis no JSON.  |
|  Conflito  |  O redimensionamento do disco é permitido apenas quando se cria uma VM ou quando a VM é desalocada.  |
|  ConflictingUserInput  |  Disco '{0}"não pode ser anexado tal como o disco já é propriedade da VM"{1}'.  |
|  ConflictingUserInput  |  Os grupos de recursos de origem e de destino são os mesmos.  |
|  ConflictingUserInput  |  As contas de armazenamento de origem e de destino do disco {0} são diferentes.  |
|  ContainerAlreadyOnLease  |  Já existe uma concessão no contentor de armazenamento que contém o blob com o URI {0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  O pedido mover recursos contém recursos KeyVault que são referenciados por um ou mais {0}s no pedido. Isto não é suportado atualmente na movimentação de subscrição cruzada. Verifique os detalhes do erro para os Ids de recurso do Cofre de chaves.  |
|  DiagnosticsOperationInternalError  |  Ocorreu um erro interno ao processar o perfil de diagnóstico de VM {0}.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Blob {0} já está a ser utilizado por outro disco pertencente VM "{1}'. Pode examinar os metadados do blob para as informações de referência do disco.  |
|  DiskBlobNotFound  |  Não é possível encontrar o blob VHD com o URI {0} para o disco "{1}'.  |
|  DiskBlobNotFound  |  Não é possível encontrar o blob VHD com o URI {0}.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} segredo não tem o {1} etiquetas. Atualize a versão secreta, adiciona as etiquetas necessárias e tente novamente.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Anular a moldagem de segredo {0} valor de utilização de chave {1} falhou.  |
|  DiskImageNotReady  |  Imagem de disco {0} está em {1} estado. Tente novamente quando a imagem está pronta.  |
|  DiskPreparationError  |  Ocorreram um ou mais erros ao preparar discos de VM. Ver a vista de instância de disco para obter detalhes.  |
|  DiskProcessingError  |  O processamento do disco foi interrompido uma vez que a VM tem outros discos nos discos com falhas.  |
|  ImageBlobNotFound  |  Não é possível encontrar o blob VHD com o URI {0} para o disco "{1}'.  |
|  ImageBlobNotFound  |  Não é possível encontrar o blob VHD com o URI {0}.  |
|  IncorrectDiskBlobType  |  Blobs de disco só podem ser do tipo blob de página. Blob {0} para o disco "{1}' é do tipo blob de blocos.  |
|  IncorrectDiskBlobType  |  Blobs de disco só podem ser do tipo blob de página. Blob {0} é do tipo "{1}'.  |
|  IncorrectImageBlobType  |  Blobs de disco só podem ser do tipo blob de página. Blob {0} para o disco "{1}' é do tipo blob de blocos.  |
|  IncorrectImageBlobType  |  Blobs de disco só podem ser do tipo blob de página. Blob {0} é do tipo "{1}'.  |
|  InternalOperationError  |  Não foi possível resolver a conta de armazenamento {0}. Certifique-se de que foi criado por meio do Provedor de recursos de armazenamento na mesma localização que o recurso de computação.  |
|  InternalOperationError  |  {0} Falha nas tarefas para atingir de objetivo.  |
|  InternalOperationError  |  Ocorreu um erro na validação do perfil de rede da VM "{0}'.  |
|  InvalidAccountType  |  O AccountType {0} é inválido.  |
|  InvalidParameter  |  O valor do parâmetro {0} é inválido.  |
|  InvalidParameter  |  A palavra-passe de Admin especificada não é permitida.  |
|  InvalidParameter  |  "A palavra-passe fornecida tem de estar entre {0}-{1} carateres de comprimento e cumprir, pelo menos, {2} dos requisitos de complexidade de palavra-passe entre as seguintes opções: <ol><li> Contém um caráter em maiúsculas</li><li>Contém um caráter em minúsculas</li><li>Contém um dígito numérico</li><li>Contém um caráter especial.</li></ol>  |
|  InvalidParameter  |  O Nome de Utilizador de Admin especificado não é permitido.  |
|  InvalidParameter  |  Não é possível anexar um disco de SO existente quando a VM é criada a partir de uma imagem de plataforma ou de utilizador.  |
|  InvalidParameter  |  Nome do contentor {0} é inválido. Os nomes de contentor tem de ter entre 3 a 63 carateres de comprimento e podem conter apenas carateres alfanuméricos minúsculos e hífenes. Hífen tem de ser precedido e seguido por um caráter alfanumérico.  |
|  InvalidParameter  |  Nome do contentor {0} no URL {1} é inválido. Os nomes de contentor tem de ter entre 3 a 63 carateres de comprimento e podem conter apenas carateres alfanuméricos minúsculos e hífenes. Hífen tem de ser precedido e seguido por um caráter alfanumérico.  |
|  InvalidParameter  |  O nome do blob no URL {0} contém uma barra. Isto atualmente não é suportado para discos.  |
|  InvalidParameter  |  O URI {0} não parece para ser um URI de blob correto.  |
|  InvalidParameter  |  Um disco com o nome '{0}"já utiliza o mesmo LUN: {1}.  |
|  InvalidParameter  |  Um disco chamado "{0}' já existe.  |
|  InvalidParameter  |  Não é possível especificar substituições da imagem de utilizador para um disco que já está definido na referência de imagem especificada.  |
|  InvalidParameter  |  Um disco com o nome '{0}"já utiliza o mesmo URL de VHD {1}.  |
|  InvalidParameter  |  A contagem de domínios de falhas especificado {0} deve estar no intervalo {1} para {2}.  |
|  InvalidParameter  |  O tipo de licença {0} é inválido. Tipos de licenças válidos são: Windows_Client ou Windows_Server, maiúsculas de minúsculas.  |
|  InvalidParameter  |  Nome do anfitrião Linux não pode exceder {0} carateres de comprimento ou conter os seguintes carateres: {1}.  |
|  InvalidParameter  |  Caminho de destino para chaves públicas Ssh está limitado para o valor predefinido {0} devido a um problema conhecido no agente de aprovisionamento do Linux.  |
|  InvalidParameter  |  Um disco no LUN {0} já existe.  |
|  InvalidParameter  |  Subscrição {0} do pedido tem de corresponder a subscrição {1} contido no id de disco gerido.  |
|  InvalidParameter  |  Dados personalizados de OSProfile têm de ser codificação Base64 e com um comprimento máximo de {0} carateres.  |
|  InvalidParameter  |  Nome do blob no URL {0} tem de terminar com "{1}" extensão.  |
|  InvalidParameter  |  {0}"não é um válido capturado VHD blob prefixo de nome. Um prefixo válido coincide com regex "{1}'.  |
|  InvalidParameter  |  Não não possível adicionar certificados para a VM se o agente da VM não está aprovisionado.  |
|  InvalidParameter  |  Um disco no LUN {0} já existe.  |
|  InvalidParameter  |  Não é possível criar a VM, porque o tamanho pedido {0} não está disponível no cluster onde o conjunto de disponibilidade é atribuído atualmente. Os tamanhos disponíveis são: {1}. Saiba mais sobre VM redimensionamento estratégia em https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  O tamanho da VM pedida {0} não está disponível na região atual. Os tamanhos disponíveis na região atual são: {1}. Saiba mais sobre os tamanhos VM disponíveis em cada região em https://aka.ms/azure-regions.  |
|  InvalidParameter  |  O tamanho da VM pedida {0} não está disponível na região atual. Saiba mais sobre os tamanhos VM disponíveis em cada região em https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Nome de utilizador de administrador do Windows não pode ser mais do que {0} caracteres longos, terminar com um ponto ou conter os seguintes carateres: {1}.  |
|  InvalidParameter  |  Nome do computador Windows não pode ser mais do que {0} caracteres longos, ser inteiramente numérico ou conter os seguintes carateres: {1}.  |
|  MissingMoveDependentResources  |  O pedido mover recursos não contém todos os recursos dependentes. Verifique os detalhes de erro de falta de ids de recurso.  |
|  MoveResourcesHaveInvalidState  |  O pedido mover recursos contém VMs que estão associadas a contas de armazenamento inválido. Verifique os detalhes para estes ids de recurso e nomes de conta de armazenamento referenciados.  |
|  MoveResourcesHavePendingOperations  |  O pedido mover recursos contém recursos para o qual está pendente uma operação. Verifique os detalhes para estes ids de recurso. Repita a operação depois de concluir as operações pendentes.  |
|  MoveResourcesNotFound  |  O pedido mover recursos contém recursos que não não possível encontrar. Verifique os detalhes para estes ids de recurso.  |
|  NetworkingInternalOperationError  |  Erro de alocação de rede desconhecido.  |
|  NetworkingInternalOperationError  |  Erro de alocação de rede desconhecido  |
|  NetworkingInternalOperationError  |  Ocorreu um erro interno no processamento do perfil de rede da VM.  |
|  NotFound  |  Conjunto de disponibilidade {0} não é possível encontrar.  |
|  NotFound  |  Máquina Virtual de origem '{0}' especificado no pedido não existe nesta localização do Azure.  |
|  NotFound  |  Inquilino com o id {0} não encontrado.  |
|  NotFound  |  A imagem {0} não é possível encontrar.  |
|  NotSupported  |  É o tipo de licença {0}, mas o blob de imagem {1} não está no local.  |
|  OperationNotAllowed  |  Conjunto de disponibilidade {0} não pode ser eliminada. Antes de eliminar um conjunto de disponibilidade, certifique-se que não contém qualquer VM.  |
|  OperationNotAllowed  |  Alterar o conjunto de disponibilidade SKU de "Alinhado" para "Clássico" não é permitida.  |
|  OperationNotAllowed  |  Não é possível modificar as extensões numa VM que não está a ser executada.  |
|  OperationNotAllowed  |  A ação de captura apenas é suportada numa máquina Virtual com discos baseados em blob. Utilize as APIs de recurso de "Imagem" para criar uma imagem de uma Máquina Virtual gerida.  |
|  OperationNotAllowed  |  O recurso {0} não é possível criar a partir da imagem {1} até que a imagem foi criada com êxito.  |
|  OperationNotAllowed  |  Atualizações para encryptionSettings não é permitida quando a VM é alocado, tente novamente após a VM é desalocada  |
|  OperationNotAllowed  |  A adição de um disco gerido a uma VM com disco baseado em blob não é suportada.  |
|  OperationNotAllowed  |  O número máximo de discos de dados podem ser anexados a uma VM deste tamanho é {0}.  |
|  OperationNotAllowed  |  A adição de um disco baseado em blob a uma VM com discos geridos não é suportada.  |
|  OperationNotAllowed  |  Operação "{0}"não é permitida na imagem"{1}' uma vez que a imagem está marcada para eliminação. Pode apenas repetir a operação de eliminação (ou aguardar que uma seja concluída).  |
|  OperationNotAllowed  |  Operação "{0}"não é permitida na VM"{1}' uma vez que a VM está generalizada.  |
|  OperationNotAllowed  |  Operação "{0}"não é permitido como a coleção de ponto de restauro"{1}' está marcado para eliminação.  |
|  OperationNotAllowed  |  Operação "{0}"não é permitida na extensão da VM"{1}' porque está marcado para eliminação. Pode apenas repetir a operação de eliminação (ou aguardar que uma seja concluída).  |
|  OperationNotAllowed  |  Operação "{0}'não é permitida porque as máquinas virtuais'{1}"estão a ser aprovisionados com a imagem"{2}'.  |
|  OperationNotAllowed  |  Operação "{0}'não é permitida porque o conjunto de dimensionamento de Máquina Virtual'{1}"está atualmente a utilizar a imagem"{2}'.  |
|  OperationNotAllowed  |  Operação "{0}"não é permitida na VM"{1}' uma vez que a VM está marcada para eliminação. Pode apenas repetir a operação de eliminação (ou aguardar que uma seja concluída).  |
|  OperationNotAllowed  |  Operação "{0}"não é permitida na VM"{1}' uma vez que a VM está desatribuída ou marcada como desatribuída.  |
|  OperationNotAllowed  |  Operação "{0}"não é permitida na VM"{1}' uma vez que a VM está em execução. O problema power desativar explicitamente caso encerre a VM a partir do sistema operativo convidado.  |
|  OperationNotAllowed  |  Operação "{0}"não é permitida na VM"{1}' uma vez que a VM não está desalocada.  |
|  OperationNotAllowed  |  Operação "{0}"não é permitida na VM"{1}", pois a VM tem a extensão'{2}' no estado de falha.  |
|  OperationNotAllowed  |  Operação "{0}"não é permitida na VM"{1}' uma vez que está em curso outra operação.  |
|  OperationNotAllowed  |  A operação "{0}'requer a Máquina Virtual'{1}" seja generalizada.  |
|  OperationNotAllowed  |  A operação requer que a VM esteja em execução (ou definida para ser executada).  |
|  OperationNotAllowed  |  Disco com tamanho {0}GB, que é inferior ao tamanho {1}GB de disco correspondente na imagem, não é permitida.  |
|  OperationNotAllowed  |  Extensões de conjunto de dimensionamento de VM do processador "{0}" podem ser adicionadas apenas no momento da criação de conjunto de dimensionamento de VM.  |
|  OperationNotAllowed  |  Extensões de conjunto de dimensionamento de VM do processador "{0}" pode ser eliminado apenas no momento da eliminação do conjunto de dimensionamento de VM.  |
|  OperationNotAllowed  |  VM '{0}' já está a utilizar discos geridos.  |
|  OperationNotAllowed  |  VM "{0}'pertence ao conjunto de disponibilidade 'Clássico' '{1}'. Atualize o conjunto de disponibilidade para utilizar o SKU "alinhado" e, em seguida, repita a conversão.  |
|  OperationNotAllowed  |  VM criada a partir da imagem não pode ter discos baseados em blob. Todos os discos têm de ser discos geridos.  |
|  OperationNotAllowed  |  A operação de recolher não pode ser concluída porque a VM não está generalizada.  |
|  OperationNotAllowed  |  Operações de gestão na VM "{0}" não são permitidas porque os discos VM estão a ser convertidos em discos geridos.  |
|  OperationNotAllowed  |  Uma operação em curso é alterar o estado de energia da Máquina Virtual {0} para {1}. Execute a operação {2} após algum tempo.  |
|  OperationNotAllowed  |  Não é possível adicionar ou atualizar a VM. O tamanho da VM pedida {0} poderá não estar disponível na unidade de alocação existente. Saiba mais sobre VM redimensionamento estratégia em https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Não foi possível redimensionar a VM, porque o tamanho pedido {0} não está disponível no cluster onde o conjunto de disponibilidade é atribuído atualmente. Os tamanhos disponíveis são: {1}. Saiba mais sobre VM redimensionamento estratégia em https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Não foi possível redimensionar a VM, porque o tamanho pedido {0} não está disponível no cluster onde a VM é atribuída atualmente. Para redimensionar a sua VM para {1} desalocar. (esta é uma operação Stop no portal do Azure) e tente novamente a operação de redimensionamento. Saiba mais sobre VM redimensionamento estratégia em https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  Falha no aprovisionamento do SO para a VM "{0}' porque o SO convidado está atualmente a ser aprovisionado.  |
|  OSProvisioningClientError  |  Aprovisionamento do SO para a VM "{0}' falhou. Detalhes do erro: {1} Certifique-se de que a imagem foi preparada corretamente (generalizada). <ul><li>Instruções para Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  Falha na geração de chaves de anfitrião SSH. Detalhes do erro: {0}. Para resolver este problema, verifique se o agente de Linux está corretamente configurado. <ul><li>Pode verificar as instruções em: https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  Nome de utilizador especificado para a VM é inválido para esta distribuição de Linux. Detalhes do erro: {0}.  |
|  OSProvisioningInternalError  |  Falha no aprovisionamento do SO para a VM "{0}' devido a um erro interno.  |
|  OSProvisioningTimedOut  |  Aprovisionamento do SO para a VM "{0}' não foi concluído no tempo atribuído. A VM poderá ainda concluir o aprovisionamento com êxito. Verifique o estado de aprovisionamento mais tarde.  |
|  OSProvisioningTimedOut  |  Aprovisionamento do SO para a VM "{0}' não foi concluído no tempo atribuído. A VM poderá ainda concluir o aprovisionamento com êxito. Verifique o estado de aprovisionamento mais tarde. Além disso, certifique-se de que a imagem foi preparada corretamente (generalizada).   <ul><li>Instruções para Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruções para Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  Aprovisionamento do SO para a VM "{0}' não foi concluído no tempo atribuído. No entanto, o agente convidado da VM foi detetado em execução. Isso sugere que o SO convidado não foi corretamente preparado para ser utilizado como uma imagem de VM (com CreateOption = FromImage). Para resolver este problema, utilize o VHD como está com CreateOption = Attach ou prepará-lo adequadamente para utilização como uma imagem:   <ul><li>Instruções para Windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruções para Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  O tamanho VM necessário não está atualmente disponível na localização selecionada.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Recursos não podem ser atualizado neste momento devido a atualização da plataforma em curso. Tente novamente mais tarde.  |
|  StorageAccountLimitation  |  Conta de armazenamento '{0}' não suporta blobs de páginas que são necessários para criar discos.  |
|  StorageAccountLimitation  |  Conta de armazenamento '{0}' excedeu a quota alocada.  |
|  StorageAccountLocationMismatch  |  Não foi possível resolver a conta de armazenamento {0}. Certifique-se de que foi criado por meio do Provedor de recursos de armazenamento na mesma localização que o recurso de computação.  |
|  StorageAccountNotFound  |  Conta de armazenamento {0} não encontrado. Certifique-se a conta de armazenamento não é eliminada e que pertence à mesma localização da VM do Azure.  |
|  StorageAccountNotRecognized  |  Utilize uma conta de armazenamento gerenciada pelo fornecedor de recursos de armazenamento. Utilizar {0} não é suportada.  |
|  StorageAccountOperationInternalError  |  Ocorreu um erro interno ao aceder à conta de armazenamento {0}.  |
|  StorageAccountSubscriptionMismatch  |  Conta de armazenamento {0} não pertence à subscrição {1}.  |
|  StorageAccountTooBusy  |  Conta de armazenamento '{0}' está atualmente demasiado ocupado. Considere utilizar outra conta.  |
|  StorageAccountTypeNotSupported  |  Disco {0} usa {1} que é uma conta de armazenamento de Blobs. Tente novamente com a conta de armazenamento de fins gerais.  |
|  StorageAccountTypeNotSupported  |  Conta de armazenamento {0} é de {1} tipo. Suporta diagnósticos de arranque {2} tipos de conta de armazenamento.  <ul><li>Este erro ocorre se utilizar a conta de armazenamento premium para o diagnóstico de arranque. Para obter mais informações, consulte [como utilizar o diagnóstico de arranque](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  A subscrição não está autorizada.  |
|  TargetDiskBlobAlreadyExists  |  Blob {0} já existe. Forneça um URI para criar um novo disco de dados em branco de blob diferente '{1}'.  |
|  TargetDiskBlobAlreadyExists  |  Captura de operação não é possível continuar porque o blob de imagem de destino {0} já existe e o sinalizador para substituir blobs de VHD não está definido. A eliminar o blob ou defina o sinalizador para substituir blobs de VHD e tente novamente.  |
|  TargetDiskBlobAlreadyExists  |  Captura de operação não é possível continuar porque o blob de imagem de destino {0} tem uma concessão ativa no mesmo.   |
|  TargetDiskBlobAlreadyExists  |  Blob {0} já existe. Forneça um URI de blob diferente como destino para o disco "{1}'.  |
|  TooManyVMRedeploymentRequests  |  Foram recebidos demasiados pedidos de reimplementação para a VM "{0}' ou as VMs no mesmo conjunto de disponibilidades com esta VM. Tente novamente mais tarde.  |
|  VHDSizeInvalid  |  O valor de tamanho de disco especificado de {0} para o disco '{1}' com o blob {2} é inválido. Tamanho do disco tem de estar entre {3} e {4}.  |
|  VMAgentStatusCommunicationError  |  VM '{0}"não comunicou o estado de agente de VM ou extensões. Verifique se a VM tem um agente VM em execução e pode estabelecer ligações de saída para o armazenamento do Azure.  |
|  VMArtifactRepositoryInternalError  |  Ocorreu um erro durante a comunicação com o repositório de artefactos para obtenção dos detalhes de artefacto da VM.  |
|  VMArtifactRepositoryInternalError  |  Ocorreu um erro interno ao obter os dados de artefacto da VM a partir do repositório de artefactos.  |
|  VMExtensionHandlerNonTransientError  |  Manipulador de '{0}'relatou uma falha para a extensão de VM'{1}'com o código de erro terminal"{2}" e mensagem de erro: "{3}'  |
|  VMExtensionManagementInternalError  |  Ocorreu um erro interno durante o processamento de extensão de VM '{0}'.  |
|  VMExtensionManagementInternalError  |  Ocorreram vários erros durante a preparação as extensões VM. Consulte a vista de instância de extensão VM para obter detalhes.  |
|  VMExtensionProvisioningError  |  VM reportou uma falha durante o processamento de extensão '{0}'. Mensagem de erro: "{1}".  |
|  VMExtensionProvisioningError  |  Várias extensões de VM falha a ser aprovisionado na VM. Consulte a vista de instância de extensão VM para obter detalhes.  |
|  VMExtensionProvisioningTimeout  |  Aprovisionamento da extensão de VM '{0}"foi excedido. A instalação da extensão poderá estar a demorar demasiado tempo ou não foi possível obter o estado da extensão.  |
|  VMMarketplaceInvalidInput  |  Criar uma máquina virtual a partir de uma imagem de tipo não mercado não necessita de informações do plano, remova as informações de plano no pedido. Nome do disco de SO é {0}.  |
|  VMMarketplaceInvalidInput  |  As informações de compra não corresponde. Não é possível implementar a imagem do Marketplace. Nome do disco de SO é {0}.  |
|  VMMarketplaceInvalidInput  |  Criar uma máquina virtual na imagem Marketplace necessita de informações de plano no pedido. Nome do disco de SO é {0}.  |
|  VMNotFound  |  A VM "{0}' não é possível encontrar.  |
|  VMRedeploymentFailed  |  VM '{0}"nova implementação falha devido a um erro interno. Tente novamente mais tarde.  |
|  VMRedeploymentTimedOut  |  Reimplementação da VM "{0}' não foi concluída no tempo atribuído. Poderá ser concluída com êxito em algum tempo. Caso contrário, pode repetir o pedido.  |
|  VMStartTimedOut  |  VM '{0}' não foram iniciados no tempo atribuído. A VM poderá ainda ser iniciada com êxito. Verifique o estado de energia mais tarde.  |


## <a name="next-steps"></a>Passos Seguintes
Se precisar de mais ajuda, pode contactar os especialistas do Azure no [os fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.