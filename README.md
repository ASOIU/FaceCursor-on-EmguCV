FaceCursor-on-EmguCV
====================
Целью данного проекта было написать приложение, использующее Emgu CV для перемещения курсора пользователя, основываясь на положении его головы относительно web-камеры. Emgu CV это кросс платформенная .net оболочка для библиотеки по обработке изображений, OpenCV, то есть она позволяет вызывать OpenCV функции из .net  совместимых языков: С#, VB и т.д. Полную информацию, об это оболочке и последние обновления, вы можете прочитать на официальном сайте разработчиков по адресу:http://www.emgu.com/wiki/index.php/Main_Page.

Приложение было написано на ЯВУ с# в интегрированной среде разработке Visual Studio, для подключения всех необходимых зависимостей  к проекту удобней воспользоваться видео руководством по адресу:https://www.youtube.com/watch?v=vdjoutNR2DQ. 

При загрузке формы создается объект класса "Capture", который находит в  списке доступных web-камер, установленную по умолчаннию, загружает  каскады Хаара, необходимых для опознания на изображении лица:
 try
            {
                capWebCam = new Capture();
            }
            catch (NullReferenceException except)
            {         
                return;
            }
            haarCasc = new HaarCascade("haarcascade_frontalface_alt2.xml");
            Application.Idle += processFrameAndUpdateGUI;
            blnCapturingProcess = true;
            Cursor.Position = new Point(SystemInformation.PrimaryMonitorSize.Width / 2, SystemInformation.PrimaryMonitorSize.Height / 2);
            scaleW = SystemInformation.PrimaryMonitorSize.Width / ibOriginal.Size.Width;
            scaleH = SystemInformation.PrimaryMonitorSize.Height / ibOriginal.Size.Height; 

так же устанавливается позиция курсора в центре экрана и задается масштаб размера экрана относительно окна вывода видео потока. Далее запускается прием изображения с вебкамеры и отрисовка его в специальном окне "ImageBox", из-за этого видно как изображение задерживается, скорость отображения несколько ниже, чем у обычных прогрммных видео. 

            using (Image<Bgr, Byte> nextFrame = capWebCam.QueryFrame())
            {
                if (nextFrame != null)
                {
                    Image<Gray, byte> grayFrame = nextFrame.Convert<Gray, byte>();
                    var faces = grayFrame.DetectHaarCascade(haarCasc,1.4,4, HAAR_DETECTION_TYPE.DO_CANNY_PRUNING,
                                                                    new Size(nextFrame.Width/8, nextFrame.Height/8))[0];
                    foreach (var face in faces)
                    {
                        nextFrame.Draw(face.rect, new Bgr(7, double.MaxValue, 0), 1);
                        Cursor.Position = new Point((int)(face.rect.X * scaleW + face.rect.Width/2), 
                            (int) (face.rect.Y *scaleH + face.rect.Height/2));                       
                    }

                    ibOriginal.Image = nextFrame;
                }
            }
В отрисованном изображении находится лицо, это лицо обводится рамкой и координаты с экрана передются курсору мышки. В целом два метода говорят о том что пользоваться библиотекой легко и следовательно развязывают руки для больших проектов.  
