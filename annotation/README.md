For now we provide the patch to annotate HBase/Hadoop; that patch for other systems will come soon.

The patch only print a line of log which consists of thread id and stage id when thread starts. You will need other tools to notify you when the thread exits (we use Byteman to do this for Java-based systems).

Based on the thread id to stage mapping, one can get stage resource consumption profile, stage relationships, etc., and further produce the TAM.
