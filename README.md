[Quotes_Items.csv](https://github.com/user-attachments/files/17604579/Quotes_Items.csv)# Анализ проекта: Quotes Scraper

## Обзор
Этот проект включает создание веб-скребка с использованием фреймворка Scrapy для сбора данных с сайта [quotes.toscrape.com](http://quotes.toscrape.com). Собранные данные включают цитаты, авторов и теги с нескольких страниц и сохраняются в файле JSON для анализа или других случаев использования.
## 1. Шаги, предпринятые в проекте (Что было сделано)
#### 1.1 Установка Python и PyCharm: Python был установлен на системе, а также среда разработки PyCharm для разработки проекта Scrapy.
#### 1.2 Создание нового проекта: В PyCharm был создан новый проект с именем quotes_scraper, в котором автоматически была настроена новая виртуальная среда.

### ![Screenshot (2191)](https://github.com/user-attachments/assets/7704cd0f-b605-4127-b29c-25af2974ddf0)

#### 1.3 Активация виртуальной среды: Виртуальная среда была активирована с помощью команды:
##### 
    .\Scripts\activate
##### (Эта команда предназначена для пользователей Windows.)
#### 1.4 Установка Scrapy: Scrapy был установлен, выполнив следующую команду:
##### 
    pip install scrapy
#### 1.5 Создание проекта Scrapy: Новый проект Scrapy был создан с помощью команды:
##### 
    scrapy startproject quotes_scraper
#### 1.6 Определил целевые страницы паука, установив start_urls на главную страницу.
##### В классе QuotesSpider я указал начальный URL для парсинга цитат. Это начальная страница сайта, с которой начинается сбор данных.
##### start_urls = [
    'http://quotes.toscrape.com/page/1/',  
]
#### 1.7 Добавил код для разбора цитат, авторов и тегов, используя CSS селекторы внутри метода parse.
##### В методе parse я использовал CSS селекторы для извлечения текста цитаты, имени автора и связанных тегов из каждого элемента на странице. Данные собираются в виде словаря и передаются с помощью yield.
##### 
    def parse(self, response):
      # Парсинг цитат
        for quote in response.css("div.quote"):
            yield {
                'text': quote.css("span.text::text").get(),
                'author': quote.css("small.author::text").get(),
                'tags': quote.css("div.tags a.tag::text").getall(),
            }
#### 1.8 Настроил паука для обработки пагинации, идентифицируя и следуя за кнопкой "Следующая страница" на каждой странице.
##### Я добавил код, который проверяет наличие ссылки на следующую страницу и, если она существует, переходит по этой ссылке, продолжая парсинг.
#####
    next_page = response.css("li.next a::attr(href)").get()
##### if next_page is not None:
    yield response.follow(next_page, self.parse)
#### 1.9 Определение моделей для собираемых данных
##### В файле items.py я определил модель для собираемых элементов, создавая класс QuotesScraperItem, который структурирует данные:
##### 
    class QuotesScraperItem(scrapy.Item):
        Text = scrapy.Field()    # Поле для текста цитаты
        Author = scrapy.Field()  # Поле для имени автора
        Tags = scrapy.Field()     # Поле для тегов цитаты
#### 1.10 После завершения разработки паука, я запустил команду для выполнения скрипта и сбора данных:
##### 
    scrapy crawl quotes
##### Мы запускаем команду scrapy crawl quotes перед созданием файла Quotes_Items.json, чтобы инициировать процесс веб-скрейпинга. По умолчанию Scrapy не создает файлы вывода автоматически; вместо этого выполняется логика скрейпинга, определенная в пауке. Чтобы сохранить собранные данные в файл, можно добавить опцию вывода (например, -o Quotes_Items.json) при запуске команды. Таким образом, данные собираются и одновременно сохраняются в указанный JSON-файл. Без запуска команды паук не выполнит скрейпинг и не создаст файл с результатами.

#### 1.11 Хранение данных:
##### Указал формат вывода как JSON с помощью:
##### 
    scrapy crawl quotes -o Quotes_Items.json

#### 1.12 Создал дополнительные выходные файлы в формате XML и CSV для анализа, чтобы обеспечить гибкость в использовании:
##### 
    scrapy crawl quotes -o Quotes_Items.xml 
    scrapy crawl quotes -o Quotes_Items.csv

## 2. Источники данных (Откуда были получены данные)
Данные были получены с публичного сайта [quotes.toscrape.com](http://quotes.toscrape.com), который предоставляет примеры цитат для тестирования приложений веб-скребка. Сайт включает несколько страниц с текстами цитат, именами авторов и связанными тегами, что позволяет обрабатывать пагинацию и собирать данные.

## 3. Процесс сбора данных (Как осуществлялся сбор)
Данные были собраны с помощью:
Определения CSS селекторов: Использовались CSS селекторы для извлечения конкретных элементов, таких как цитаты (span.text), авторы (span.author) и теги (.tag). Каждая цитата сохранялась как элемент, содержащий эти поля.
Обработки пагинации: Паука следил за кнопкой "Следующая страница", извлекая ее ссылку и рекурсивно вызывая метод parse, пока все страницы не были собраны.
Каждая цитата с соответствующим автором и тегами возвращалась как словарь (элемент Scrapy) и сохранялась в указанные форматы.

## 4. Выбор методов/инструментов (Почему был выбран тот или иной метод/инструмент, а не другой)
Почему Scrapy?
Эффективность: Scrapy предназначен для веб-скребка и автоматизирует многие аспекты (запросы, ответы и хранение данных).
Встроенная пагинация: Легко обрабатывает многопагинаторные запросы, что является ключевым для этого проекта.
Опции экспорта данных: Позволяет сохранять данные непосредственно в формате JSON, что идеально подходит для анализа.
Почему CSS селекторы?
Простота: CSS селекторы интуитивно понятны для структуры HTML этого сайта.
Производительность: CSS селекторы, как правило, быстрее, чем XPath, что делает их идеальными, когда они могут достигать того же результата.

## Заключение
Этот проект на основе Scrapy успешно собрал цитаты, авторов и теги с нескольких страниц сайта, используя пагинацию для захвата всего контента. Выбор Scrapy и CSS селекторов был обусловлен необходимостью использования эффективного инструмента для скребка и простотой CSS селекторов для структуры сайта.

[UploAuthor,Tags,Text
Albert Einstein,"change,deep-thoughts,thinking,world",“The world as we have created it is a process of our thinking. It cannot be changed without changing our thinking.”
J.K. Rowling,"abilities,choices","“It is our choices, Harry, that show what we truly are, far more than our abilities.”"
Albert Einstein,"inspirational,life,live,miracle,miracles",“There are only two ways to live your life. One is as though nothing is a miracle. The other is as though everything is a miracle.”
Jane Austen,"aliteracy,books,classic,humor","“The person, be it gentleman or lady, who has not pleasure in a good novel, must be intolerably stupid.”"
Marilyn Monroe,"be-yourself,inspirational","“Imperfection is beauty, madness is genius and it's better to be absolutely ridiculous than absolutely boring.”"
Albert Einstein,"adulthood,success,value",“Try not to become a man of success. Rather become a man of value.”
André Gide,"life,love",“It is better to be hated for what you are than to be loved for what you are not.”
Thomas A. Edison,"edison,failure,inspirational,paraphrased","“I have not failed. I've just found 10,000 ways that won't work.”"
Eleanor Roosevelt,misattributed-eleanor-roosevelt,“A woman is like a tea bag; you never know how strong it is until it's in hot water.”
Steve Martin,"humor,obvious,simile","“A day without sunshine is like, you know, night.”"
Marilyn Monroe,"friends,heartbreak,inspirational,life,love,sisters","“This life is what you make it. No matter what, you're going to mess up sometimes, it's a universal truth. But the good part is you get to decide how you're going to mess it up. Girls will be your friends - they'll act like it anyway. But just remember, some come, some go. The ones that stay with you through everything - they're your true best friends. Don't let go of them. Also remember, sisters make the best friends in the world. As for lovers, well, they'll come and go too. And baby, I hate to say it, most of them - actually pretty much all of them are going to break your heart, but you can't give up because if you give up, you'll never find your soulmate. You'll never find that half who makes you whole and that goes for everything. Just because you fail once, doesn't mean you're gonna fail at everything. Keep trying, hold on, and always, always, always believe in yourself, because if you don't, then who will, sweetie? So keep your head high, keep your chin up, and most importantly, keep smiling, because life's a beautiful thing and there's so much to smile about.”"
J.K. Rowling,"courage,friends","“It takes a great deal of bravery to stand up to our enemies, but just as much to stand up to our friends.”"
Albert Einstein,"simplicity,understand","“If you can't explain it to a six year old, you don't understand it yourself.”"
Bob Marley,love,"“You may not be her first, her last, or her only. She loved before she may love again. But if she loves you now, what else matters? She's not perfect—you aren't either, and the two of you may never be perfect together but if she can make you laugh, cause you to think twice, and admit to being human and making mistakes, hold onto her and give her the most you can. She may not be thinking about you every second of the day, but she will give you a part of her that she knows you can break—her heart. So don't hurt her, don't change her, don't analyze and don't expect more than she can give. Smile when she makes you happy, let her know when she makes you mad, and miss her when she's not there.”"
Dr. Seuss,fantasy,"“I like nonsense, it wakes up the brain cells. Fantasy is a necessary ingredient in living.”"
Douglas Adams,"life,navigation","“I may not have gone where I intended to go, but I think I have ended up where I needed to be.”"
Elie Wiesel,"activism,apathy,hate,indifference,inspirational,love,opposite,philosophy","“The opposite of love is not hate, it's indifference. The opposite of art is not ugliness, it's indifference. The opposite of faith is not heresy, it's indifference. And the opposite of life is not death, it's indifference.”"
Friedrich Nietzsche,"friendship,lack-of-friendship,lack-of-love,love,marriage,unhappy-marriage","“It is not a lack of love, but a lack of friendship that makes unhappy marriages.”"
Mark Twain,"books,contentment,friends,friendship,life","“Good friends, good books, and a sleepy conscience: this is the ideal life.”"
Allen Saunders,"fate,life,misattributed-john-lennon,planning,plans",“Life is what happens to us while we are making other plans.”
Pablo Neruda,"love,poetry","“I love you without knowing how, or when, or from where. I love you simply, without problems or pride: I love you in this way because I do not know any other way of loving but this, in which there is no I or you, so intimate that your hand upon my chest is my hand, so intimate that when I fall asleep your eyes close.”"
Ralph Waldo Emerson,happiness,“For every minute you are angry you lose sixty seconds of happiness.”
Mother Teresa,attributed-no-source,"“If you judge people, you have no time to love them.”"
Garrison Keillor,"humor,religion",“Anyone who thinks sitting in church can make you a Christian must also think that sitting in a garage can make you a car.”
Jim Henson,humor,“Beauty is in the eye of the beholder and it may be necessary from time to time to give a stupid or misinformed beholder a black eye.”
Dr. Seuss,"comedy,life,yourself","“Today you are You, that is truer than true. There is no one alive who is Youer than You.”"
Albert Einstein,"children,fairy-tales","“If you want your children to be intelligent, read them fairy tales. If you want them to be more intelligent, read them more fairy tales.”"
J.K. Rowling,,"“It is impossible to live without failing at something, unless you live so cautiously that you might as well not have lived at all - in which case, you fail by default.”"
Albert Einstein,imagination,“Logic will get you from A to Z; imagination will get you everywhere.”
Bob Marley,music,"“One good thing about music, when it hits you, you feel no pain.”"
Dr. Seuss,"learning,reading,seuss","“The more that you read, the more things you will know. The more that you learn, the more places you'll go.”"
J.K. Rowling,dumbledore,"“Of course it is happening inside your head, Harry, but why on earth should that mean that it is not real?”"
Bob Marley,friendship,"“The truth is, everyone is going to hurt you. You just got to find the ones worth suffering for.”"
Mother Teresa,"misattributed-to-mother-teresa,paraphrased",“Not all of us can do great things. But we can do small things with great love.”
J.K. Rowling,"death,inspirational","“To the well-organized mind, death is but the next great adventure.”"
Charles M. Schulz,"chocolate,food,humor",“All you need is love. But a little chocolate now and then doesn't hurt.”
William Nicholson,"misattributed-to-c-s-lewis,reading",“We read to know we're not alone.”
Albert Einstein,"knowledge,learning,understanding,wisdom",“Any fool can know. The point is to understand.”
Jorge Luis Borges,"books,library",“I have always imagined that Paradise will be a kind of library.”
George Eliot,inspirational,“It is never too late to be what you might have been.”
George R.R. Martin,"read,readers,reading,reading-books","“A reader lives a thousand lives before he dies, said Jojen. The man who never reads lives only one.”"
C.S. Lewis,"books,inspirational,reading,tea",“You can never get a cup of tea large enough or a book long enough to suit me.”
Marilyn Monroe,,“You believe lies so you eventually learn to trust no one but yourself.”
Marilyn Monroe,"girls,love","“If you can make a woman laugh, you can make her do anything.”"
Albert Einstein,"life,simile","“Life is like riding a bicycle. To keep your balance, you must keep moving.”"
Marilyn Monroe,love,“The real lover is the man who can thrill you by kissing your forehead or smiling into your eyes or just staring into space.”
Marilyn Monroe,attributed-no-source,"“A wise girl kisses but doesn't love, listens but doesn't believe, and leaves before she is left.”"
Martin Luther King Jr.,"hope,inspirational",“Only in the darkness can you see the stars.”
J.K. Rowling,dumbledore,"“It matters not what someone is born, but what they grow to be.”"
James Baldwin,love,"“Love does not begin and end the way we seem to think it does. Love is a battle, love is a war; love is a growing up.”"
Jane Austen,"friendship,love","“There is nothing I would not do for those who are really my friends. I have no notion of loving people by halves, it is not my nature.”"
Eleanor Roosevelt,"attributed,fear,inspiration",“Do one thing every day that scares you.”
Marilyn Monroe,attributed-no-source,"“I am good, but not an angel. I do sin, but I am not the devil. I am just a small girl in a big world trying to find someone to love.”"
Albert Einstein,music,"“If I were not a physicist, I would probably be a musician. I often think in music. I live my daydreams in music. I see my life in terms of music.”"
Haruki Murakami,"books,thought","“If you only read the books that everyone else is reading, you can only think what everyone else is thinking.”"
Alexandre Dumas fils,misattributed-to-einstein,“The difference between genius and stupidity is: genius has its limits.”
Stephenie Meyer,"drug,romance,simile","“He's like a drug for you, Bella.”"
Ernest Hemingway,"books,friends,novelist-quotes",“There is no friend as loyal as a book.”
Helen Keller,inspirational,"“When one door of happiness closes, another opens; but often we look so long at the closed door that we do not see the one which has been opened for us.”"
George Bernard Shaw,"inspirational,life,yourself",“Life isn't about finding yourself. Life is about creating yourself.”
Charles Bukowski,alcohol,"“That's the problem with drinking, I thought, as I poured myself a drink. If something bad happens you drink in an attempt to forget; if something good happens you drink in order to celebrate; and if nothing happens you drink to make something happen.”"
Suzanne Collins,the-hunger-games,“You don’t forget the face of the person who was your last hope.”
Suzanne Collins,humor,"“Remember, we're madly in love, so it's all right to kiss me anytime you feel like it.”"
C.S. Lewis,love,"“To love at all is to be vulnerable. Love anything and your heart will be wrung and possibly broken. If you want to make sure of keeping it intact you must give it to no one, not even an animal. Wrap it carefully round with hobbies and little luxuries; avoid all entanglements. Lock it up safe in the casket or coffin of your selfishness. But in that casket, safe, dark, motionless, airless, it will change. It will not be broken; it will become unbreakable, impenetrable, irredeemable. To love is to be vulnerable.”"
J.R.R. Tolkien,"bilbo,journey,lost,quest,travel,wander",“Not all those who wander are lost.”
J.K. Rowling,live-death-love,"“Do not pity the dead, Harry. Pity the living, and, above all those who live without love.”"
Ernest Hemingway,"good,writing",“There is nothing to writing. All you do is sit down at a typewriter and bleed.”
Ralph Waldo Emerson,"life,regrets",“Finish each day and be done with it. You have done what you could. Some blunders and absurdities no doubt crept in; forget them as soon as you can. Tomorrow is a new day. You shall begin it serenely and with too high a spirit to be encumbered with your old nonsense.”
Mark Twain,education,“I have never let my schooling interfere with my education.”
Dr. Seuss,troubles,“I have heard there are troubles of more than one kind. Some come from ahead and some come from behind. But I've bought a big bat. I'm all ready you see. Now my troubles are going to have troubles with me!”
Alfred Tennyson,"friendship,love",“If I had a flower for every time I thought of you...I could walk through my garden forever.”
Charles Bukowski,humor,“Some people never go crazy. What truly horrible lives they must lead.”
Terry Pratchett,"humor,open-mind,thinking","“The trouble with having an open mind, of course, is that people will insist on coming along and trying to put things in it.”"
Dr. Seuss,"humor,philosophy","“Think left and think right and think low and think high. Oh, the thinks you can think up if only you try!”"
J.D. Salinger,"authors,books,literature,reading,writing","“What really knocks me out is a book that, when you're all done reading it, you wish the author that wrote it was a terrific friend of yours and you could call him up on the phone whenever you felt like it. That doesn't happen much, though.”"
George Carlin,"humor,insanity,lies,lying,self-indulgence,truth",“The reason I talk to myself is because I’m the only one whose answers I accept.”
John Lennon,"beatles,connection,dreamers,dreaming,dreams,hope,inspirational,peace","“You may say I'm a dreamer, but I'm not the only one. I hope someday you'll join us. And the world will live as one.”"
W.C. Fields,"humor,sinister",“I am free of all prejudice. I hate everyone equally. ”
Ayn Rand,,“The question isn't who is going to let me; it's who is going to stop me.”
Mark Twain,"books,classic,reading",“′Classic′ - a book which people praise and don't read.”
Albert Einstein,mistakes,“Anyone who has never made a mistake has never tried anything new.”
Jane Austen,"humor,love,romantic,women","“A lady's imagination is very rapid; it jumps from admiration to love, from love to matrimony in a moment.”"
J.K. Rowling,integrity,"“Remember, if the time should come when you have to make a choice between what is right and what is easy, remember what happened to a boy who was good, and kind, and brave, because he strayed across the path of Lord Voldemort. Remember Cedric Diggory.”"
Jane Austen,"books,library,reading","“I declare after all there is no enjoyment like reading! How much sooner one tires of any thing than of a book! -- When I have a house of my own, I shall be miserable if I have not an excellent library.”"
Jane Austen,"elizabeth-bennet,jane-austen","“There are few people whom I really love, and still fewer of whom I think well. The more I see of the world, the more am I dissatisfied with it; and every day confirms my belief of the inconsistency of all human characters, and of the little dependence that can be placed on the appearance of merit or sense.”"
C.S. Lewis,"age,fairytales,growing-up",“Some day you will be old enough to start reading fairy tales again.”
C.S. Lewis,god,“We are not necessarily doubting that God will do the best for us; we are wondering how painful the best will turn out to be.”
Mark Twain,"death,life",“The fear of death follows from the fear of life. A man who lives fully is prepared to die at any time.”
Mark Twain,"misattributed-mark-twain,truth",“A lie can travel half way around the world while the truth is putting on its shoes.”
C.S. Lewis,"christianity,faith,religion,sun","“I believe in Christianity as I believe that the sun has risen: not only because I see it, but because by it I see everything else.”"
J.K. Rowling,truth,"“The truth."" Dumbledore sighed. ""It is a beautiful and terrible thing, and should therefore be treated with great caution.”"
Jimi Hendrix,"death,life","“I'm the one that's got to die when it's time for me to die, so let me live my life the way I want to.”"
J.M. Barrie,"adventure,love",“To die will be an awfully big adventure.”
E.E. Cummings,courage,“It takes courage to grow up and become who you really are.”
Khaled Hosseini,life,“But better to get hurt by the truth than comforted with a lie.”
Harper Lee,better-life-empathy,“You never really understand a person until you consider things from his point of view... Until you climb inside of his skin and walk around in it.”
Madeleine L'Engle,"books,children,difficult,grown-ups,write,writers,writing","“You have to write the book that wants to be written. And if the book will be too difficult for grown-ups, then you write it for children.”"
Mark Twain,truth,“Never tell the truth to people who are not worthy of it.”
Dr. Seuss,inspirational,"“A person's a person, no matter how small.”"
George R.R. Martin,"books,mind","“... a mind needs books as a sword needs a whetstone, if it is to keep its edge.”"
ading Quotes_Items.csv…]()



