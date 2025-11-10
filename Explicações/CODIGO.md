Eu fiz em um ambiente CONDA local! ⋆
  Voce pode tambem tentar fazer no COLAB, vou mostrar das duas formas. ⋆𐙚₊˚⊹♡

  Segue a explicação detalhada sobre o código para tentar sanar possíveis dúvidas:

<div align="center"> <a href="https://git.io/typing-svg"> <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=500&size=22&pause=1000000000&color=FFBFEF&center=true&vCenter=true&random=false&width=524&lines=%E2%8A%B9+1-+O+que+esse+codigo+faz?+%E2%8A%B9+" alt="Typing SVG"> </a> </div><img align="center" alt="" src="./src/header-gif.gif">
Esse projeto combina YOLO (Ultralytics) e PaddleOCR para detectar objetos em um vídeo (por exemplo, embalagens em uma linha de produção), recortar as áreas de interesse (ROIs) e verificar automaticamente datas de produção, validade e código de lote.

O sistema reconhece os textos dessas áreas, compara com os valores esperados e salva frames de sucesso (onde pelo menos um campo está correto), registrando erros consecutivos e totais para monitoramento.

<div align="center"> <a href="https://git.io/typing-svg"> <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&weight=500&size=22&pause=1000000000&color=FFBFEF&center=true&vCenter=true&random=false&width=524&lines=Passo+a+passo+do+codigo:+" alt="Typing SVG"> </a> </div><img align="center" alt="" src="./src/header-gif.gif">


 <h3 align="center" style="color:#FFBFEF;">1️- IMPORTAÇÃO DAS BIBLIOTECAS </h3>

  
   -      import cv2 
          from ultralytics import solutions, YOLO 
          import logging 
          import torch 
          import queue 
          import re 
          from paddleocr import PaddleOCR 
          import threading 
          import os 
          from datetime import datetime


Essas bibliotecas são responsáveis por:

1- cv2 (OpenCV) → leitura e manipulação de imagens/vídeos.

2- YOLO (Ultralytics) → detecção de objetos em tempo real.

3- PaddleOCR → reconhecimento de texto (OCR).

4- threading / queue → processamento paralelo (OCR em outra thread).

5- logging / datetime / os / re / torch → controle de logs, datas, arquivos e expressões regulares. <br>


# <h3 align="center" style="color:#FFBFEF;"> 2- CONFIGURAÇÃO DO OCR</h3>

Aqui o código inicializa o OCR.

-      paddleocr = PaddleOCR(
        lang="en",
        show_log=False,
        use_gpu=torch.cuda.is_available(),
        enable_mkldnn=False,
        log_level="ERROR"
      )




1- lang="en" define o idioma do reconhecimento (pode mudar para "pt" se quiser testar português).

2- use_gpu ativa a GPU automaticamente se disponível.

3- show_log=False evita que o PaddleOCR polua o terminal.

4- Se der erro, ele captura a exceção e continua o programa sem travar. 


# <h3 align="center" style="color:#FFBFEF;">3️- VARIÁVEIS GLOBAIS E CONTADORES</h3>


-      ocr_queue = queue.Queue()
       target_dates = ['251124', '210925'] # data de producao e data de validade
       arget_code = '2024330' #codigo do lote


1- Esses valores representam o que o sistema deve encontrar nas imagens.

2- target_dates = lista com datas esperadas (exemplo: produção e validade).

3- target_code = código do lote.

4- ocr_queue = fila usada para enviar imagens para o worker do OCR.

5- O código também define contadores de erro:

-   *_consecutive_errors = erros seguidos (se atingir 2, gera alerta crítico).

       *_total_errors = erros acumulados ao longo do vídeo (nunca zera).




# <h3 align="center" style="color:#FFBFEF;">4️- THREAD DO OCR</h3>


-      def ocr_worker():
    


Essa função roda em paralelo, processando as imagens enviadas pelo YOLO.

Ela:

1- Pega uma imagem da fila (ocr_queue.get()).

2- Usa o PaddleOCR para extrair o texto e a confiança média.

3- Remove tudo que não for número (re.sub(r'\\D', '', text)).

4- Compara com as datas e código de lote esperados.

5- Atualiza os contadores de erro.

6- Salva frames de sucesso em uma pasta (success_frames/).

Isso evita travamentos no loop principal e garante que o vídeo continue sendo processado sem atrasos.







# <h3 align="center" style="color:#FFBFEF;">5️- FUNÇÃO DE EXTRAÇÃO DE TEXTO</h3>


-      def extract_text_from_image(image):
      ...


Aqui o código:

1- Salva a ROI como temp_ocr.jpg.

2- Passa essa imagem para o paddleocr.ocr() que retorna o texto e a confiança.

3- Faz a média das confianças das palavras detectadas.

4- Remove o arquivo temporário depois do uso (finally).






# <h3 align="center" style="color:#FFBFEF;">6️- CONFIGURAÇÃO DO YOLO</h3>


-      cap = cv2.VideoCapture("video.mp4")
       yolo_model = YOLO(model_path)
       counter = solutions.ObjectCounter(...)


1- cap abre o vídeo que será processado. <br>

2- yolo_model carrega o modelo YOLO (best.pt ou o padrão yolov8n.pt). <br>

3- ObjectCounter é uma solução pronta da Ultralytics que faz:

   -  Rastreamento de objetos (tracking).

4- Contagem de quantos passam por uma região da imagem (region). <br>

5- A região é definida por coordenadas (nesse caso, parte direita da tela).






# <h3 align="center" style="color:#FFBFEF;">7️- LOOP PRINCIPAL DE PROCESSAMENTO</h3>


-      while cap.isOpened():
        success, im0 = cap.read()


Enquanto o vídeo estiver aberto, ele:

  -Lê cada frame (im0).

  -Redimensiona para 640x640.

  -Passa o frame pelo YOLO (counter(frame_resized)).

  -Para cada novo objeto detectado, recorta a região (ROI) e envia para a fila OCR.

  -Assim que o objeto cruza a linha definida, ele é capturado e processado.





# <h3 align="center"> 8️- LÓGICA DE VERIFICAÇÃO </h3>

Dentro do worker, a imagem é analisada e:

  -Se o texto contiver a data e o lote esperados = sucesso 

  -Se algum campo faltar = incrementa erro 

  -Se houver 2 falhas seguidas no mesmo campo = alerta crítico

  -Frames bem-sucedidos são salvos com timestamp em success_frames/.







# <h3 align="center" style="color:#FFBFEF;">9️- FINALIZAÇÃO DO PROGRAMA</h3>

Quando o vídeo termina ou o usuário interrompe:

1- shutdown_flag é ativado.

2- O código aguarda o fim da fila e encerra a thread.

3- Mostra estatísticas finais no terminal:

-  Total de imagens com sucesso.

-  Erros por campo.

-  Quantos objetos foram detectados.

