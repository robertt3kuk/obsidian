  
  

func parse(ctx context.Context, url string) ([]string, error){

  

}

  

func main(){

ctx,cancel:=context.WitTimeout(context.Background(),time.Minute)

defer cancel()

urls,err:=parse(context.Background(),"https://google.com")

if err!=nil{

log.Fatalf("error parsing url %v",err)

}

  

maxProc:=5

crawler = Crawler{}

done = crawler.Start(ctx)

  

<-done

}

  

type Crawler struct {

links map[string]int // смотреть на уникальность

tasks chan(string)

updateTasks chan([]string)

allTasks []string

mw sync.Mutex

}

  
  

func (c *Crawler)Start(ctx context.Context, urls []string, maxProc int)chan(bool){

c.links = make(map[string]int)

c.tasks = make(chan(string),maxProc)

c.updateTasks = make(chan[string],maxProc)

c.allTasks = urls

done = make(chan(bool))

  

go func(){

for {

mw.Lock()

if len(c.allTasks)==0{

done <- true

return

}

c.allTasks[0]-> c.tasks

c.allTasks = c.allTasks[1:]

mw.Unlock()

}

}()

  

for i<c.maxProc{

go c.Process(ctx)

i++

}

  

return done

}

  

func (c *Crawler)Process(ctx context.Context){

for url <- c.tasks{

result,err:=parse(ctx,url)

if err!=nil{

continue

}

c.updateTasks <- result

}

}

  

func (c *Crawler)ProcessUpdate([]string){

for retTasks <- c.updateTasks{

res:=[]string{}

for _,c := range retTasks{

if _,ok:=c.links[c];!ok{

res = append(res,c)

c.links[c] = 0

}

}

c.mw.Lock()

c.allTasks = append(c.allTasks,res...)

c.mw.Unlock()

}

}