# working draft

Lets understand how linux pipes work?
syntax
```bash
prog1 arg | prog2 arg
```

```bash
n=1
while true; do
  echo $n
  ((n++))
  sleep 1  
done
```

```bash
while read num; do
  echo "Read number: $num"
done
```

```
bash wr.sh | bash re.sh
```
What happens if program one is writing faster than program 2 is reading?
When prog2 is slow than prog1 is writing content is put in a buffer and writer is blocked until reader resumes.Lets modify above program.

```bash
# fast writer
n=1
while true; do
  echo "Writing: $n"
  echo $n
  ((n++))
done
```

```bash
# Slow reader
while read num; do
  echo "Read number: $num"
  sleep 1 # add soem delay to reading
done
```
This reader will read the data slowly to simulate a slow consumer, adding a delay between each read.

At the beginning, the fast writer will write numbers into the pipe quickly.
Pipe buffer fills: Once the pipe buffer (~64 KB) fills up, the writer will be paused, waiting for the reader to consume data.
Slow consumption: The reader reads one number every second (sleep 1), slowly freeing space in the buffer.
Writer waits: The writer continues only when the reader consumes enough data to free up space in the buffer.

The OS ensures that the writer and reader remain synchronized through this buffering mechanism, preventing data loss while managing the flow between fast and slow processes.

DO programs run councurrently?
On ifrst glance it might seem that prog2 run after pro1 has completed running because of mental model of pipes. But both programs run concurrently 


But at what point data goes from one program to anoter?
Out from one program is buffered before out  being sent to another program. To avoid this programs can use unbuffered IO using `fflush`

