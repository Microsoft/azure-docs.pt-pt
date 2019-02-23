# [Documentação dos Serviços de Multimédia](index.md)

# [Descrição geral](media-services-overview.md)
## [Cenários e disponibilidade](scenarios-and-availability.md)
## [Conceitos](media-services-concepts.md)

# Introdução
## [Criar e gerir a conta](media-services-portal-create-account.md)
## [Configurar o ambiente de desenvolvimento](media-services-set-up-computer.md)
### [.NET](media-services-dotnet-how-to-use.md)
### [REST](media-services-rest-how-to-use.md)  
## [Utilizar a autenticação do Azure AD para aceder à API](media-services-use-aad-auth-to-access-ams-api.md)
### [Utilizar o portal para gerir a autenticação do Azure AD](media-services-portal-get-started-with-aad.md)
### [Aceder à API com .NET](media-services-dotnet-get-started-with-aad.md)
### [Aceder à API com REST](media-services-rest-connect-with-aad.md)
### [Utilizar a CLI do Azure para criar e configurar uma aplicação do Azure AD](media-services-cli-create-and-configure-aad-app.md)
### [Utilizar o Azure PowerShell para criar e configurar uma aplicação do Azure AD](media-services-powershell-create-and-configure-aad-app.md)

## Entregar vídeo a pedido
### [Portal do Azure](media-services-portal-vod-get-started.md)
### [SDK do .NET](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## Realizar transmissão em direto
### [Portal do Azure](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# Procedimento
## Gerir
### Entidades
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [Pontos finais de transmissão em fluxo](media-services-streaming-endpoints-overview.md)
#### [Portal do Azure](media-services-portal-manage-streaming-endpoints.md)
#### [.NET](media-services-dotnet-manage-streaming-endpoints.md)
### Armazenamento
#### [Atualizar Serviços de Multimédia após a implementação de chaves de acesso ao armazenamento](media-services-roll-storage-access-keys.md)
#### [Gerir recursos entre várias contas de armazenamento](meda-services-managing-multiple-storage-accounts.md)
### [Quotas e limitações](media-services-quotas-and-limitations.md)
## [Configurar o Postman](media-rest-apis-with-postman.md)
### [Coleção de transmissão em fluxo a pedido](postman-collection.md)
### [Coleção de transmissão em fluxo ao vivo](postman-live-streaming-collection.md)
### [Ambiente](postman-environment.md)
## Carregar conteúdo
### Carregar ficheiros para uma conta
#### [Portal do Azure](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [Carregar ficheiros grandes com Aspera](media-services-upload-files-with-aspera.md)
### [Carregar ficheiros com o StorSimple](media-services-upload-files-from-storsimple.md)
### [Copiar blobs existentes](media-services-copying-existing-blob.md)

## [Codificar conteúdo](media-services-encode-asset.md)
### [Comparar codificadores](media-services-compare-encoders.md)
### [Gerir a velocidade e simultaneidade da codificação](media-services-manage-encoding-speed.md)
### Media Encoder Standard (MES)
#### [Formatos e Codecs do Media Encoder Standard](media-services-media-encoder-standard-formats.md)
#### [Utilize o MES para gerar automaticamente uma escala de bits](media-services-autogen-bitrate-ladder-with-mes.md)
#### Codificar com o Media Encoder Standard
##### [Portal do Azure](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
##### [REST](media-services-rest-encode-asset.md)
#### [Codificação avançada com MES](media-services-advanced-encoding-with-mes.md)
##### [Personalizar as predefinições do Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
##### [Como gerar miniaturas com o Media Encoder Standard com o .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
##### [Recortar vídeos com o Media Encoder Standard](media-services-crop-video.md)
##### [Gerar um sprite de miniatura](generate-thumbnail-sprite.md)
#### Esquemas MES
##### [Esquema Media Encoder Standard](media-services-mes-schema.md)
##### [Metadados de entrada](media-services-input-metadata-schema.md)
##### [Metadados de saída](media-services-output-metadata-schema.md)
#### [Predefinições MES](media-services-mes-presets-overview.md) 
##### [H264 Taxas de Bits Múltiplas 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)
##### [H264 Taxas de Bits Múltiplas 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)
##### [H264 Taxas de Bits Múltiplas 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Taxas de Bits Múltiplas 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)
##### [H264 Taxas de Bits Múltiplas 16x9 para iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)
##### [H264 Taxas de Bits Múltiplas 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)
##### [H264 Taxas de Bits Múltiplas 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)
##### [H264 Taxas de Bits Múltiplas 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Taxas de Bits Múltiplas 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)
##### [H264 Taxas de Bits Múltiplas 4x3 para iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)
##### [H264 Taxas de Bits Múltiplas 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)
##### [H264 Taxas de Bits Múltiplas 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)
##### [H264 Taxa de Bits Única 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)
##### [H264 Taxa de Bits Única 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md)
##### [H264 Taxa de Bits Única 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Taxa de Bits Única 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)
##### [H264 Taxa de Bits Única 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)
##### [H264 Taxa de Bits Única 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md)
##### [H264 Taxa de Bits Única 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Taxa de Bits Única 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)
##### [H264 Taxa de Bits Única 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)
##### [H264 Taxa de Bits Única 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md)
##### [H264 Taxa de Bits Única 720p para Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)
##### [H264 Taxa de Bits Única de Alta Qualidade SD para Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)
##### [H264 Taxa de Bits Única de Baixa Qualidade SD para Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)
### Fluxo de Trabalho Premium de Codificador de Multimédia
#### [Formatos e Codecs de Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-premium-workflow-encoder-formats.md)
#### Codificar com o Fluxo de Trabalho Premium do Codificador de Multimédia
##### [Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-encode-with-premium-workflow.md)
##### [Tutoriais de Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-media-encoder-premium-workflow-tutorials.md)
##### [Criar Fluxos de Trabalho de Codificação Avançados com o Estruturador de Fluxos de Trabalho](media-services-workflow-designer.md)
##### [Fluxo de trabalho Premium com várias entradas](media-services-media-encoder-premium-workflow-multiplefilesinput.md)
### [Criar uma tarefa que gera segmentos fMP4](media-services-generate-fmp4-chunks.md)
### Processadores de multimédia
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)
### [Códigos de erro](media-services-encoding-error-codes.md)
### Preterido
#### [Empacotamento e encriptação estáticas](media-services-static-packaging.md)

## [Transmissão em direto](media-services-manage-channels-overview.md)
### [Codificadores locais](media-services-live-streaming-with-onprem-encoders.md)
#### [Codificadores locais recomendados](media-services-recommended-encoders.md)
#### [Portal do Azure](media-services-portal-live-passthrough-get-started.md)
#### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
### [Transmissão em direto com codificador em cloud](media-services-manage-live-encoder-enabled-channels.md)
#### [Portal do Azure](media-services-portal-creating-live-encoder-enabled-channel.md)
#### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
### [Configurar codificadores no local para utilização com codificadores em cloud](media-services-live-encoders-overview.md)
#### [Codificador de FMLE](media-services-configure-fmle-live-encoder.md)
#### [Codificador Haivision KB](media-services-configure-kb-live-encoder.md)
#### [Codificador de NewTek TriCaster](media-services-configure-tricaster-live-encoder.md)
#### [Codificador de Wirecast](media-services-configure-wirecast-live-encoder.md)
### [Lidar com operações de longa duração](media-services-dotnet-long-operations.md)
### [Especificação de ingestão em tempo real de MP4 fragmentado](media-services-fmp4-live-ingest-overview.md)

<!-- ## [Clip content](media-services-azure-media-clipper-overview.md)
### [Getting started](media-services-azure-media-clipper-getting-started.md)
### [Load videos](media-services-azure-media-clipper-load-assets.md)
### [Configure keyboard shortcuts](media-services-azure-media-clipper-keyboard-shortcuts.md)
### [Configure localization](media-services-azure-media-clipper-localization.md)
### [Submit clipping jobs](media-services-azure-media-clipper-submit-job.md)
### [Azure portal](media-services-azure-media-clipper-portal.md) -->

## [Proteger conteúdo](media-services-content-protection-overview.md)
### [Encriptação do armazenamento](media-services-rest-storage-encryption.md)
### [Encriptação de AES-128](media-services-protect-with-aes128.md)
### [PlayReady/Widevine para Transmissão em fluxo](media-services-protect-with-playready-widevine.md)
### [FairPlay para Transmissão em fluxo](media-services-protect-hls-with-fairplay.md)
### [Offline PlayReady para Windows 10](offline-playready-streaming-windows-10.md)
### [Offline FairPlay para iOS](media-services-protect-hls-with-offline-fairplay.md)
### [Offline Widevine para Android](offline-widevine-for-android.md)
### [Configurar no portal do Azure](media-services-portal-protect-content.md)
### [Distribuir licenças de DRM](media-services-deliver-keys-and-licenses.md)
### Criar ContentKeys
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### Descrições gerais do modelo de licenças
#### [Modelo de licença PlayReady](media-services-playready-license-template-overview.md)
#### [Modelo de licença do Widevine](media-services-widevine-license-template-overview.md)
### Configurar políticas de entrega de ativo
#### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
#### [REST](media-services-rest-configure-asset-delivery-policy.md)
### Configure a política de autorização da chave de conteúdo
#### [Portal do Azure](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)
### [Transmita os tokens de autenticação para AMS](media-services-pass-authentication-tokens.md)
### Estruturas de referência
#### [Estrutura de sistema de DRM híbrido](hybrid-design-drm-sybsystem.md)
#### [Estrutura de múltiplos DRM de referência](media-services-cenc-with-multidrm-access-control.md)

## [Analisar](media-services-analytics-overview.md)
### [Analisar multimédia através do portal do Azure](media-services-portal-analyze.md)
### [Processar com o Indexador 2](media-services-process-content-with-indexer2.md)
### [Processar com o Indexador](media-services-index-content.md)
#### [Predefinição de tarefas](indexer-task-preset.md)
### [Processar com o Hyperlapse](media-services-hyperlapse-content.md)
### [Processar com o Detetor de Rostos](media-services-face-and-emotion-detection.md)
### [Processar com o Detetor de Movimento](media-services-motion-detection.md)
### [Processar com o Editor de Rostos](media-services-face-redaction.md)
#### [Instruções de Redação de Rostos](media-services-redactor-walkthrough.md)
### [Processar com miniaturas de vídeos](media-services-video-summarization.md)
### [Processar com o OCR](media-services-video-optical-character-recognition.md)
### [Processar com o Content Moderator](media-services-content-moderation.md)

## [Configurar telemetria](media-services-telemetry-overview.md)
### [.NET](media-services-dotnet-telemetry.md)
### [REST](media-services-rest-telemetry.md)

## Escala
### [Processamento de Multimédia](media-services-scale-media-processing-overview.md)
#### [Portal do Azure](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
### Pontos Finais de Transmissão em fluxo
#### [Portal do Azure](media-services-portal-scale-streaming-endpoints.md)

## [Entregar conteúdo](media-services-deliver-content-overview.md)
### [Empacotamento dinâmico](media-services-dynamic-packaging-overview.md)
### [Descrição geral dos filtros e dos manifestos dinâmicos](media-services-dynamic-manifest-overview.md)
#### [Criar filtros com .NET](media-services-dotnet-dynamic-manifest.md)
#### [Criar filtros com REST](media-services-rest-dynamic-manifest.md)
### [Política de Colocação em Cache de CDN na Extensão de Serviços de Multimédia](../../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### Publicar conteúdo
#### [Portal do Azure](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [Entregar por Transferência](media-services-deliver-asset-download.md)
### [Cenário de transmissão de ativação pós-falha](media-services-implement-failover.md)

## Consumir
### [Reproduzir multimédia com leitores existentes](media-services-playback-content-with-existing-players.md)
### [Reproduzir multimédia com Leitor de Multimédia](media-services-develop-video-players.md)
### Outras opções de reprodução
#### [Aplicação da Loja Windows de transmissão em fluxo uniforme](media-services-build-smooth-streaming-apps.md)
#### [Aplicação HTML5 com DASH.js](media-services-embed-mpeg-dash-in-html5.md)
#### [Leitores Open Source Media Framework da Adobe](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [Inserir publicidade no lado do cliente](media-services-inserting-ads-on-client-side.md)
### [Licenciamento do Kit de Migração de Cliente de Smooth Streaming da Microsoft](media-services-sspk.md)

## Integrar
### [Utilizar as Funções do Azure com os Serviços de Multimédia](media-services-dotnet-how-to-use-azure-functions.md)
### [Funções do Azure com exemplos dos Serviços de Multimédia](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

## Monitorizar
### Verificar progresso da tarefa
#### [REST](media-services-rest-check-job-progress.md)
#### [Portal do Azure](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Monitorizar notificações de tarefas com armazenamento de filas](media-services-dotnet-check-job-progress-with-queues.md)
### [Monitorizar notificações de tarefas com webhooks](media-services-dotnet-check-job-progress-with-webhooks.md)

## Resolução de problemas
### [Perguntas mais frequentes](media-services-frequently-asked-questions.md)
### [Guia de resolução de problemas da transmissão em direto](media-services-troubleshooting-live-streaming.md)
### [Códigos de erro](media-services-error-codes.md)
### [Repetir a lógica](media-services-retry-logic-in-dotnet-sdk.md)

# Referência
## [Exemplos de código](https://azure.microsoft.com/resources/samples/?service=media-services)
## [Azure PowerShell (Resource Manager)](/powershell/module/az.media)
## [Azure PowerShell (Gestão de Serviço)](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0)
## [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client)
## [REST](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
## Especificações
### [Ingestão em Direto - Especificação de ingestão em tempo real de MP4 fragmentado](media-services-fmp4-live-ingest-overview.md)
### [Ingestão Dinâmica - Assinalar Metadados Temporizados na Transmissão em Direto](media-services-specifications-live-timed-metadata.md)
### [Smooth Streaming HEVC](media-services-specifications-ms-sstr-amendment-hevc.md)

# Recursos
## [Comunidade dos Serviços de Multimédia do Azure](media-services-community.md)
## [Mapa do Azure](https://azure.microsoft.com/roadmap/?category=web-mobile)
## [Preços](https://azure.microsoft.com/pricing/details/media-services/)
## [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)
## [Notas de versão](media-services-release-notes.md)
## [Vídeos](https://azure.microsoft.com/resources/videos/index/?services=media-services)
