---
title: Códigos de erro VM comuns em Azure / Microsoft Docs
description: Compreenda alguns dos códigos de erro comuns encontrados quando fornece e gere máquinas virtuais em Azure
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
ms.openlocfilehash: 52508a6820ce0cbbbe3a0341a99894f8b92b1645
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87831231"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Compreender as mensagens de erro comuns na gestão de máquinas virtuais no Azure

Este artigo descreve alguns dos códigos e mensagens de erro mais comuns que pode encontrar quando cria ou gere máquinas virtuais (VMs) em Azure.

>[!NOTE]
> Pode deixar comentários nesta página para obter feedback ou através [do feedback do Azure](https://feedback.azure.com/forums/216843-virtual-machines) com #azerrormessage tag.

## <a name="error-response-format"></a>Formato de resposta a erros 
Os VMs Azure utilizam o seguinte formato JSON para resposta a erros:

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

Uma resposta de erro inclui sempre um código de estado e um objeto de erro. Cada objeto de erro contém sempre um código de erro e uma mensagem. Se o VM for criado com um modelo, o objeto de erro também contém uma secção de detalhes que contém um nível interno de códigos de erro e mensagem. Normalmente, o nível mais interno de mensagem de erro é a falha da raiz.


## <a name="common-virtual-machine-management-errors"></a>Erros comuns de gestão de máquinas virtuais

Esta secção lista as mensagens de erro comuns que pode encontrar ao gerir os VMs:

|  Código de Erro  |  Mensagem de Erro  |  
|  :------| :-------------|  
|  AdquirirDiskLeaseFailed  |  Não conseguiu adquirir o arrendamento enquanto criava o disco {0} ' usando blob com URI {1} . Blob já está a ser usado.  |  
|  AtribuiçãoFailada  |  A atribuição falhou. Tente reduzir o tamanho de VM ou o número de VMs, tente voltar a trabalhar para um conjunto de disponibilidade diferente ou localização Azure diferente.  |  
|  AtribuiçãoFailada  |  A atribuição de VM falhou devido a um erro interno. Por favor, tente mais tarde ou tente implementar para um local diferente.  |
|  ArtifactNotFound  |  A extensão VM com editor {0} ' e tipo ' ' não foi encontrada no local {1} {2} '.  |
|  ArtifactNotFound  |  A extensão com a {0} versão ', tipo ' e tipo handler ' do editor não foi encontrada no {1} {2} repositório de extensão.  |
|  ArtifactVersionNotFound  |  Nenhuma versão encontrada no repositório de artefactos que satisfaça a versão {0} solicitada'.  |
|  ArtifactVersionNotFound  |  Nenhuma versão encontrada no repositório de artefactos que satisfaça a versão solicitada {0} ' para extensão VM com editor {1} ' e tipo ' {2} .  |
|  AnexadoDiskWhileBeingDetached  |  Não é possível anexar o disco de dados {0} ' 'a VM {1} ' 'porque o disco está atualmente a ser desligado. Por favor, aguarde até que o disco esteja completamente desapegado e tente novamente.  |
|  BadRequest  |  Alinhamento' Os conjuntos de disponibilidade ainda não são suportados nesta região.  |
|  BadRequest  |  Não é suportada a adição de um VM com discos geridos a Conjunto de Disponibilidade não gerido ou a adição de um VM com discos à base de blob para o Conjunto de Disponibilidade gerido. Por favor, crie um Conjunto de Disponibilidade com conjunto de propriedades 'geridos' de forma a adicionar um VM com discos geridos.  |
|  BadRequest  |  Os Discos Geridos não são suportados nesta região.  |
|  BadRequest  |  Múltiplas VMExtensions por manipulador não suportados para o tipo de SO {0} '. VMExtension ' {1} 'with handler {2} ' já adicionado ou especificado na entrada.  |
|  BadRequest  |  A Operação {0} ' não é suportada em Recursos ' {1} ' com discos geridos.  |
|  CertificadoImproperlyFormada  |  A representação JSON do segredo recuperada tem um campo de {0} dados que não é um ficheiro PFX devidamente formatado, ou a palavra-passe fornecida não descodifica corretamente o ficheiro PFX.  |
|  CertificadoImproperlyFormada  |  Os dados {0} obtidos não são desercializáveis no JSON.  |
|  Conflito  |  O redimensionamento do disco só é permitido quando se cria um VM ou quando o VM é transacionado.  |
|  Inpute-se conflituoso  |  O disco {0} ' ' não pode ser ligado, uma vez que o disco já é propriedade de VM {1} '.  |
|  Inpute-se conflituoso  |  Os grupos de recursos de origem e destino são os mesmos.  |
|  Inpute-se conflituoso  |  As contas de armazenamento de fonte e destino para o disco {0} são diferentes.  |
|  ContainerAlreadyOnLease  |  Já existe um arrendamento no recipiente de armazenamento que mantém a bolha com {0} URI.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  O pedido de recursos move contém recursos KeyVault que são referenciados por um ou mais {0} s no pedido. Isto não é suportado atualmente no Movimento de Subscrição Cross. Verifique os detalhes de erro dos Ids de recurso KeyVault.  |
|  DiagnósticoOperationInternalError  |  Ocorreu um erro interno durante o processamento do perfil de diagnóstico de VM {0} .  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  Blob {0} já está a ser utilizado por outro disco pertencente à VM'. {1} Pode examinar os metadados blob para obter informações de referência do disco.  |
|  DiskBlobNotFound  |  Incapaz de encontrar bolha VHD com URI {0} para o {1} disco'.  |
|  DiskBlobNotFound  |  Incapaz de encontrar bolha VHD com URI {0} .  |
|  DiskEncryptionKeySecretMissingTags  |  {0} segredo não tem as {1} etiquetas. Por favor, atualize a versão secreta, adicione as etiquetas necessárias e revendo.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Desembrulhar o valor secreto {0} usando a chave {1} falhou.  |
|  DiskImageNotReady  |  A imagem do {0} disco está no {1} estado. Por favor, reda o momento em que a imagem estiver pronta.  |
|  DiskPreparationError  |  Ocorreram um ou mais erros durante a preparação dos discos VM. Consulte a visualização do caso do disco para mais detalhes.  |
|  DiskProcessingError  |  O processamento do disco parou porque o VM tem outros discos em discos falhados.  |
|  ImagemBlobNotFound  |  Incapaz de encontrar bolha VHD com URI {0} para o {1} disco'.  |
|  ImagemBlobNotFound  |  Incapaz de encontrar bolha VHD com URI {0} .  |
|  IncorrectDiskBlobType  |  As bolhas de disco só podem ser de bolha de página tipo. Blob {0} para disco ' é de tipo bloco {1} blob.  |
|  IncorrectDiskBlobType  |  As bolhas de disco só podem ser de bolha de página tipo. Blob {0} é do tipo ' {1} .  |
|  IncorrectImageBlobType  |  As bolhas de disco só podem ser de bolha de página tipo. Blob {0} para disco ' é de tipo bloco {1} blob.  |
|  IncorrectImageBlobType  |  As bolhas de disco só podem ser de bolha de página tipo. Blob {0} é do tipo ' {1} .  |
|  InternalOperationError  |  Não consegui resolver a conta de {0} armazenamento. Certifique-se de que foi criado através do Fornecedor de Recursos de Armazenamento no mesmo local que o recurso compute.  |
|  InternalOperationError  |  {0} objetivo procurando tarefas falhou.  |
|  InternalOperationError  |  Erro ocorreu na validação do perfil de rede de VM {0} '.  |
|  InvalidAccountType  |  O 'AccountType' {0} é inválido.  |
|  InvalidParameter  |  O valor do parâmetro {0} é inválido.  |
|  InvalidParameter  |  A palavra-passe Admin especificada não é permitida.  |
|  InvalidParameter  |  "A palavra-passe fornecida deve ser entre {0} - {1} caracteres longos e deve satisfazer pelo menos {2} os requisitos de complexidade da palavra-passe a partir do seguinte: <ol><li> Contém um caráter maiúscula</li><li>Contém um caráter minúsculo</li><li>Contém um dígito numérico</li><li>Contém um caráter especial.</li></ol>  |
|  InvalidParameter  |  O nome de utilizador Admin especificado não é permitido.  |
|  InvalidParameter  |  Não é possível anexar um disco de OS existente se o VM for criado a partir de uma plataforma ou imagem do utilizador.  |
|  InvalidParameter  |  O nome do recipiente {0} é inválido. Os nomes dos recipientes devem ter 3-63 caracteres de comprimento e podem conter apenas caracteres alfanuméricos e hífen. O hífen deve ser precedido e seguido por um carácter alfanumérico.  |
|  InvalidParameter  |  O nome do recipiente {0} em URL {1} é inválido. Os nomes dos recipientes devem ter 3-63 caracteres de comprimento e podem conter apenas caracteres alfanuméricos e hífen. O hífen deve ser precedido e seguido por um carácter alfanumérico.  |
|  InvalidParameter  |  O nome blob em URL {0} contém um corte. Atualmente, isto não é suportado para discos.  |
|  InvalidParameter  |  O URI {0} não parece estar correto blob URI.  |
|  InvalidParameter  |  Um disco chamado {0} ' ' ' já usa o mesmo LUN: {1} .  |
|  InvalidParameter  |  Um disco chamado {0} ' ' já existe.  |
|  InvalidParameter  |  Não é possível especificar as substituições de imagem do utilizador para um disco já definido na referência de imagem especificada.  |
|  InvalidParameter  |  Um disco chamado {0} ' ' já usa o mesmo URL VHD {1} .  |
|  InvalidParameter  |  A contagem de domínio de avarias especificada {0} deve cair no intervalo para {1} {2} .  |
|  InvalidParameter  |  O tipo de licença {0} é inválido. Os tipos de licença válidos são: Windows_Client ou Windows_Server, caso sensível a caso.  |
|  InvalidParameter  |  O nome do anfitrião Linux não pode exceder {0} os caracteres de comprimento ou conter os seguintes caracteres: {1} .  |
|  InvalidParameter  |  O caminho de destino das chaves públicas Ssh está atualmente limitado ao seu valor padrão {0}  devido a um problema conhecido no agente de provisionamento Linux.  |
|  InvalidParameter  |  Um disco na LUN {0} já existe.  |
|  InvalidParameter  |  A subscrição {0} do pedido deve corresponder à subscrição contida no {1} id do disco gerido.  |
|  InvalidParameter  |  Os dados personalizados no OSProfile devem estar na codificação base64 e com um comprimento máximo de {0} caracteres.  |
|  InvalidParameter  |  O nome blob em URL {0} deve terminar com ' ' {1} extensão.  |
|  InvalidParameter  |  {0}' não é um prefixo de nome vhd capturado válido. Um prefixo válido corresponde ao {1} regex' .  |
|  InvalidParameter  |  Os certificados não podem ser adicionados ao seu VM se o agente VM não for a provisionado.  |
|  InvalidParameter  |  Um disco na LUN {0} já existe.  |
|  InvalidParameter  |  Não é possível criar o VM porque o tamanho solicitado {0} não está disponível no cluster onde o conjunto de disponibilidade está atualmente atribuído. Os tamanhos disponíveis são: {1} . Leia mais sobre a estratégia de redimensionamento de VM em https://aka.ms/azure-resizevm .  |
|  InvalidParameter  |  A dimensão VM solicitada {0} não está disponível na região atual. Os tamanhos disponíveis na região atual são: {1} . Saiba mais sobre os tamanhos VM disponíveis em cada região em https://aka.ms/azure-regions .  |
|  InvalidParameter  |  A dimensão VM solicitada {0} não está disponível na região atual. Saiba mais sobre os tamanhos VM disponíveis em cada região em https://aka.ms/azure-regions .  |
|  InvalidParameter  |  O nome de utilizador do administrador do Windows não pode ser mais do que {0} caracteres longos, terminar com um período(.) ou conter os seguintes caracteres: {1} .  |
|  InvalidParameter  |  O nome do computador windows não pode ser mais do que {0} caracteres longos, ser inteiramente numérico, ou conter os seguintes caracteres: {1} .  |
|  MissingMoveDependentResources  |  O pedido de recursos de mudança não contém todos os recursos dependentes. Verifique os detalhes de erros para identificação de recursos em falta.  |
|  Estado De MoveResourcesHaveInvalidState  |  O pedido de Recursos Move contém VMs que estão associados a contas de armazenamento inválidas. Verifique os detalhes destes ids de recursos e nomes de conta de armazenamento referenciados.  |
|  MoveResourcesHavePendingOperations  |  O pedido de recursos de mudança contém recursos para os quais está pendente uma operação. Por favor, verifique os detalhes destes ids de recursos. Redoem a sua operação assim que as operações pendentes estiverem concluídas.  |
|  MoveResourcesNotFound  |  O pedido de recursos de mudança contém recursos que não podem ser encontrados. Por favor, verifique os detalhes destes ids de recursos.  |
|  NetworkingInternalOperationError  |  Erro de atribuição de rede desconhecido.  |
|  NetworkingInternalOperationError  |  Erro de atribuição de rede desconhecido  |
|  NetworkingInternalOperationError  |  Ocorreu um erro interno no perfil de rede de processamento do VM.  |
|  NotFound  |  O Conjunto de Disponibilidade {0} não pode ser encontrado.  |
|  NotFound  |  A Máquina Virtual de Origem {0} ' especificada no pedido não existe nesta localização Azure.  |
|  NotFound  |  Inquilino com identificação {0} não encontrado.  |
|  NotFound  |  A Imagem {0} não pode ser encontrada.  |
|  Não suportado  |  O tipo de licença é {0} , mas a bolha de imagem não é do {1} local.  |
|  OperaçãoNotAllowed  |  Disponibilidade Definida {0} não pode ser eliminada. Antes de eliminar um Conjunto de Disponibilidade, certifique-se de que não contém nenhum VM.  |
|  OperaçãoNotAllowed  |  Não é permitida a alteração do conjunto de disponibilidade SKU de 'Alinhado' para 'Classic'.  |
|  OperaçãoNotAllowed  |  Não é possível modificar extensões no VM quando o VM não está em funcionamento.  |
|  OperaçãoNotAllowed  |  A ação Captura só é suportada numa Máquina Virtual com discos baseados em bolhas. Por favor, utilize as APIs de recurso 'Image' para criar uma Imagem a partir de uma Máquina Virtual gerida.  |
|  OperaçãoNotAllowed  |  O recurso {0} não pode ser criado a partir da Imagem até que a Imagem tenha sido criada com {1} sucesso.  |
|  OperaçãoNotAllowed  |  Atualizações para encriptaçãoSS não são permitidas quando vM é atribuído, por favor, reagem após vM ser transcativado  |
|  OperaçãoNotAllowed  |  A adição de um disco gerido a um VM com discos com bolhas não é suportada.  |
|  OperaçãoNotAllowed  |  O número máximo de discos de dados autorizados a ser anexados a um VM deste tamanho é {0} .  |
|  OperaçãoNotAllowed  |  A adição de um disco à base de bolhas em VM com discos geridos não é suportada.  |
|  OperaçãoNotAllowed  |  A Operação {0} ' ' não é permitida na Imagem ' ' uma vez que a imagem está marcada para {1} eliminação. Só pode voltar a tentar a operação Eliminar (ou esperar que uma em curso esteja concluída).  |
|  OperaçãoNotAllowed  |  A operação ' {0} ' não é permitida em VM ' ' uma vez que o {1} VM é generalizado.  |
|  OperaçãoNotAllowed  |  A operação ' {0} ' não é permitida, uma vez que a recolha do ponto de restauro {1} ' está marcada para a eliminação.  |
|  OperaçãoNotAllowed  |  A operação ' {0} ' não é permitida na extensão VM ' ' uma vez que está marcada para {1} eliminação. Só pode voltar a tentar a operação Eliminar (ou esperar que uma em curso esteja concluída).  |
|  OperaçãoNotAllowed  |  A operação {0} ' ' não é permitida uma vez que as Máquinas Virtuais {1} ' ' estão sendo a provisionadas usando a imagem {2} '.  |
|  OperaçãoNotAllowed  |  O funcionamento {0} ' ' não é permitido uma vez que o Virtual Machine ScaleSet {1} ' ' está atualmente a usar a imagem {2} '.  |
|  OperaçãoNotAllowed  |  A operação ' {0} ' não é permitida em VM ' ' uma vez que o {1} VM está marcado para eliminação. Só pode voltar a tentar a operação Eliminar (ou esperar que uma em curso esteja concluída).  |
|  OperaçãoNotAllowed  |  A operação {0} ' ' não é permitida em VM ' ' uma vez que o {1} VM é deallocado ou marcado para ser negociado.  |
|  OperaçãoNotAllowed  |  A operação {0} ' ' não é permitida em VM ' ' uma vez que o {1} VM está em execução. Por favor, desligue explicitamente o VM de dentro do sistema operativo de hóspedes.  |
|  OperaçãoNotAllowed  |  A operação ' {0} ' não é permitida em VM {1} '' uma vez que o VM não é transatado.  |
|  OperaçãoNotAllowed  |  A operação ' {0} ' não é permitida em VM ' ' uma vez que {1} VM tem extensão {2} ' ' em estado falhado.  |
|  OperaçãoNotAllowed  |  A operação ' {0} ' não é permitida em VM ' ' uma vez que outra {1} operação está em curso.  |
|  OperaçãoNotAllowed  |  A operação {0} ' ' requer que a Máquina Virtual ' ' seja {1} generalizada.  |
|  OperaçãoNotAllowed  |  A operação requer que o VM esteja em funcionamento (ou programado para funcionar).  |
|  OperaçãoNotAllowed  |  Não é permitido o disco com tamanho {0} GB, que é menor do que o tamanho {1} GB do disco correspondente na Imagem.  |
|  OperaçãoNotAllowed  |  As extensões do conjunto de escala VM do manipulador {0} ' ' só podem ser adicionadas no momento da criação do Conjunto de Escala VM.  |
|  OperaçãoNotAllowed  |  As extensões do conjunto de escala VM do manipulador {0} ' ' só podem ser eliminadas no momento da eliminação da escala VM.  |
|  OperaçãoNotAllowed  |  VM {0} ' ' já está usando discos geridos.  |
|  OperaçãoNotAllowed  |  VM {0} ' 'pertence ao conjunto de disponibilidade 'Classic' {1} '. Por favor, atualize o conjunto de disponibilidade para utilizar o SKU 'Alinhado' e, em seguida, re-tentar a Conversão.  |
|  OperaçãoNotAllowed  |  O VM criado a partir de Imagem não pode ter discos à base de bolhas. Todos os discos têm de ser geridos discos.  |
|  OperaçãoNotAllowed  |  A operação de captura não pode ser concluída porque o VM não é generalizado.  |
|  OperaçãoNotAllowed  |  As operações de gestão em VM {0} ' ' são proibidas porque os discos VM estão a ser convertidos para discos geridos.  |
|  OperaçãoNotAllowed  |  Uma operação em curso está a mudar o estado de energia da Máquina Virtual {0} para {1} . Por favor, execute a operação {2} depois de algum tempo.  |
|  OperaçãoNotAllowed  |  Não é possível adicionar ou atualizar o VM. O tamanho de VM solicitado {0} pode não estar disponível na unidade de atribuição existente. Leia mais sobre a estratégia de redimensionamento de VM em https://aka.ms/azure-resizevm .  |
|  OperaçãoNotAllowed  |  Não é possível redimensionar o VM porque a dimensão solicitada {0} não está disponível no cluster onde o conjunto de disponibilidade está atualmente atribuído. Os tamanhos disponíveis são: {1} . Leia mais sobre a estratégia de redimensionamento de VM em https://aka.ms/azure-resizevm .  |
|  OperaçãoNotAllowed  |  Não é possível redimensionar o VM porque a dimensão solicitada {0} não está disponível no cluster onde o VM está atualmente atribuído. Para redimensionar o seu VM {1} para, por favor, transar (isto é stop operation no portal Azure) e tentar novamente o redimensionar. Leia mais sobre a estratégia de redimensionamento de VM em https://aka.ms/azure-resizevm .  |
|  OSProvisioningClientError  |  O Provisioning os OS falhou para vm {0} ' 'porque o SO convidado está atualmente a ser a provisionado.  |
|  OSProvisioningClientError  |  O fornecimento de OS para VM {0} ' 'falhou. Detalhes de erro: {1} Certifique-se de que a imagem foi devidamente preparada (generalizada). <ul><li>Instruções para windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  A geração chave do anfitrião SSH falhou. Detalhes de erro: {0} . Para resolver este problema, verifique se o agente Linux está devidamente configurado. <ul><li>Pode consultar as instruções em: https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  O nome de utilizador especificado para o VM é inválido para esta distribuição Linux. Detalhes de erro: {0} .  |
|  OSProvisioningInternalError  |  O provisionamento do OS falhou para vm {0} ' ' devido a um erro interno.  |
|  OSProvisioningTimedOut  |  Os Provisões para VM {0} ' ' não terminaram no tempo atribuído. O VM pode ainda terminar o fornecimento com sucesso. Por favor, verifique o estado de provisionamento mais tarde.  |
|  OSProvisioningTimedOut  |  Os Provisões para VM {0} ' ' não terminaram no tempo atribuído. O VM pode ainda terminar o fornecimento com sucesso. Por favor, verifique o estado de provisionamento mais tarde. Além disso, certifique-se de que a imagem foi devidamente preparada (generalizada).   <ul><li>Instruções para [o Windows]( ../windows/upload-generalized-managed.md).</li><li> Instruções para [Linux](../linux/capture-image.md)</li></ul>  |
|  OSProvisioningTimedOut  |  Os Provisões para VM {0} ' ' não terminaram no tempo atribuído. No entanto, o agente convidado VM foi detetado a funcionar. Isto sugere que o SO convidado não foi devidamente preparado para ser usado como uma imagem VM (com CreateOption=FromImage). Para resolver este problema, utilize o VHD como está com o CreateOption=Attach ou prepare-o corretamente para ser utilizado como uma imagem:   <ul><li>Instruções para windows: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Instruções para Linux: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  O tamanho VM necessário não está atualmente disponível no local selecionado.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  O recurso não pode ser atualizado neste momento devido à atualização da plataforma em curso. Tente novamente mais tarde.  |
|  ArmazenamentoSAdeslimitação  |  A conta de armazenamento {0} ' ' não suporta bolhas de página que são necessárias para criar discos.  |
|  ArmazenamentoSAdeslimitação  |  A conta de armazenamento {0} ' ' ' excedeu a sua quota atribuída.  |
|  ArmazenamentoCocountLocationMismatch  |  Não consegui resolver a conta de {0} armazenamento. Certifique-se de que foi criado através do Fornecedor de Recursos de Armazenamento no mesmo local que o recurso compute.  |
|  ArmazenamentoAccountNotFound  |  Conta de {0} armazenamento não encontrada. Certifique-se de que a conta de armazenamento não é eliminada e pertence à mesma localização Azure que o VM.  |
|  ArmazenamentoAccountNot Reconhecido  |  Utilize uma conta de armazenamento gerida pelo Fornecedor de Recursos de Armazenamento. A utilização {0} não é suportada.  |
|  ArmazenamentoOperatórioPernalError  |  Ocorreu um erro interno durante o acesso à conta de armazenamento {0} .  |
|  StorageAccountSubscriptionMismatch  |  A conta de armazenamento {0} não pertence à {1} subscrição.  |
|  ArmazenamentoTobusy  |  A conta de armazenamento {0} ' ' está muito ocupada atualmente. Considere usar outra conta.  |
|  ArmazenamentoAccountTypeNotsupported  |  O {0} disco utiliza uma conta de armazenamento {1} Blob. Por favor, ressaça com a conta de armazenamento de propósito geral.  |
|  ArmazenamentoAccountTypeNotsupported  |  A conta {0} de armazenamento é do {1} tipo. O Boot Diagnostics suporta {2} tipos de conta de armazenamento.  <ul><li>Este erro ocorre se utilizar a conta de armazenamento premium para diagnósticos boot. Para obter mais informações, consulte [Como utilizar diagnósticos de arranque.](boot-diagnostics.md) </li></ul> |
|  SubscriçãoNotAuthorizedForImage  |  A subscrição não está autorizada.  |
|  TargetDiskBlobAlreadyExists  |  Blob {0} já existe. Por favor, forneça um URI blob diferente para criar um novo disco de dados em {1} branco'.  |
|  TargetDiskBlobAlreadyExists  |  A operação de captura não pode continuar porque a bolha de imagem alvo {0} já existe e a bandeira para substituir bolhas VHD não está definida. Ou apaga a bolha ou coloca a bandeira para substituir as bolhas VHD e voltar a tentar.  |
|  TargetDiskBlobAlreadyExists  |  A operação de captura não pode continuar porque a mancha de imagem alvo {0} tem um arrendamento ativo.   |
|  TargetDiskBlobAlreadyExists  |  Blob {0} já existe. Por favor, forneça um URI blob diferente como alvo para o {1} disco'  |
|  TooManyVMRedeploymentRequests  |  Foram recebidos demasiados pedidos de reafectação para os VM {0} ' ou os VMs no mesmo conjunto de disponibilidade com este VM. Por favor, reda o novo tempo mais tarde.  |
|  VHDSizeInvalid  |  O valor especificado do tamanho do disco {0} para o disco ' com bolha é {1} {2} inválido. O tamanho do disco deve estar entre {3} e {4} . .  |
|  VMAgentStatusCommunicationError  |  VM {0} ' ' não reportou o estado do agente VM ou extensões. Verifique se o VM tem um agente VM em execução e pode estabelecer ligações de saída para o armazenamento Azure.  |
|  VMArtifactRepositoryInternalError  |  Ocorreu um erro durante a comunicação com o repositório de artefactos para recuperar detalhes do artefacto VM.  |
|  VMArtifactRepositoryInternalError  |  Ocorreu um erro interno durante a recuperação dos dados do artefacto VM do repositório de artefactos.  |
|  VMExtensionHandlerNonTransientError  |  Handler ' {0} ' reportou falha para extensão VM {1} ' ' com código de erro terminal ' ' e {2} mensagem de erro: {3} ' '  |
|  VMExtensionManagementInternalError  |  Ocorreu um erro interno durante o processamento da extensão {0} VM'.  |
|  VMExtensionManagementInternalError  |  Ocorreram vários erros durante a preparação das extensões VM. Consulte a visualização da instância de extensão VM para obter mais detalhes.  |
|  VMExtensionProvisioningError  |  A VM relatou uma falha no processamento {0} da extensão'. Mensagem de erro: {1} ".  |
|  VMExtensionProvisioningError  |  Várias extensões VM não foram previstas no VM. Consulte a vista da instância de extensão VM para mais detalhes.  |
|  VMExtensionProvisioningTimeout  |  O provisionamento da extensão VM {0} ' ' '' tem prazo esgotado. A instalação de extensão pode demorar demasiado tempo ou não foi possível obter o estado de extensão.  |
|  VMMarketplaceInvalidInput  |  A criação de uma máquina virtual a partir de uma imagem não marketplace não precisa de informações do Plano, por favor remova as informações do Plano no pedido. O nome do disco de SO é {0} .  |
|  VMMarketplaceInvalidInput  |  A informação de compra não coincide. Incapaz de implantar a partir da imagem do Marketplace. O nome do disco de SO é {0} .  |
|  VMMarketplaceInvalidInput  |  A criação de uma máquina virtual a partir da imagem do Marketplace requer informações do Plano no pedido. O nome do disco de SO é {0} .  |
|  VMNotFound  |  O VM {0} ' não pode ser encontrado.  |
|  VMRedeploymentFailed  |  A reafectação de VM {0} ' falhou devido a um erro interno. Por favor, reda o novo tempo mais tarde.  |
|  VMRedeploymentTimedOut  |  A recolocação de VM {0} ' ' não terminou no tempo atribuído. Pode terminar com sucesso em algum momento. Caso contrário, pode voltar a tentar o pedido.  |
|  VMStartTimedOut  |  VM {0} ' ' não começou no tempo atribuído. O VM ainda pode começar com sucesso. Por favor, verifique o estado de energia mais tarde.  |


## <a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.