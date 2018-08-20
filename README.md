<h1><strong>Introduction</strong></h1>
<p>At Aiko Mail, we work extensively with email technologies such as IMAP, which facilitates the management of messages
  within user inboxes. To facilitate use of the IMAP protocol, we use a number of modules and processes under the hood
  as part of our open source Mouseion library. One such module, coined Post Office 2, is an internally developed module
  that improves on our open source Post Office library and deals with low level IMAP operations that act as bottlenecks.
</p>
<p>As part of this task, you will be asked to implement a subset of Post Office 2 in the form of a daemon where<strong>
    you will code a wrapper around an existing IMAP library.</strong> Please note that implementing the IMAP portion is
  <strong>strictly outside of the scope</strong> of this task. This is meant to be a simpler exercise in writing a
  production-level wrapper upon an existing IMAP library of your choosing.</p>
<h1>Assessment</h1>
<p><strong>Your daemon should expose the wrapper in the form of an API (hint: WebSockets will be the fastest here) that
    our test suite can interact with.</strong></p>
<p>We will check your solution for the following:</p>
<ul>
  <li>
    <p><strong>Design</strong>: How did you lay out your solution? If you used a library besides MailKit or rust-imap,
      what did you choose and did you make a good choice in doing so? What are some drawbacks in your approach? Is it
      capable of asynchronous input, and if not, would it benefit from doing so?</p>
    <ul>
      <li>
        <p>Tip: there are two key forms of identifying messages in a sequence on a mailserver: UID and ID. You should
          prefer UID.</p>
      </li>
    </ul>
  </li>
  <li>
    <p><strong>Speed</strong>: We utilize a number of benchmarks to test the speed of your solution, and place the
      highest weight on fetching and decoding large amounts of emails, in part or in full.</p>
    <ul>
      <li>
        <p>You're not required to make your process async or multiprocessed, as it will be run in its own process
          anyhow, so your code can live on the main thread. Doing so may increase the speed of your implementation in
          which case you may which to do so. However, we have received fully synchronous solutions in the past that are
          faster than some asynchronous solutions.</p>
      </li>
      <li>
        <p>Tip: the two most common calls in our test suite are both synchronization based. The first synchronizes
          flags/existence for messages already fetched by the test suite (e.g. checking if a message has been read or
          deleted). The second checks for new messages or fetches messages in part of in full by range.</p>
      </li>
      <li>
        <p>The relevant RFCs are 2683 (which discusses efficient implementation) and 4549 (which described sync
          implementation). They're really long and I don't recommend reading them unless you're lost and need context as
          to what sort of operations the test suite will be undertaking.</p>
      </li>
      <li>
        <p>If the latter case applies (but it really shouldn't), the most useful part of the dozens of RFCs for IMAP is
          RFC4549 sections 4 (https://datatracker.ietf.org/doc/html/rfc4549#section-4) and 6.1
          (https://datatracker.ietf.org/doc/html/rfc4549#section-6.1). Section 4 discusses a simple sync implementation
          that the test suite defaults to. Section 6.1 is a sync implementation for when CONDSTORE is available (and
          much more efficient). More on CONDSTORE below:</p>
      </li>
      <li>
        <p><strong>The CONDSTORE extension</strong>: if you google around you may hear about CONDSTORE, which is an
          efficient version of synchronization for IMAP that most libraries support. <strong>You are not required to
            include this in your wrapper! However, we will award extra points to solutions that correctly implement
            this.</strong> Keep in mind this is extraordinarily difficult to implement and you are best to leave this
          for last.</p>
      </li>
    </ul>
  </li>
  <li>
    <p><strong>Stability</strong>: Your solution will be tested within Aiko Mail's state-of-the-art CryoMX stack, which
      is capable of fast-forwarding mailservers to simulate use over long periods of time as well as situations such as
      downtime, offline access, and command conflicts. Your solution should be capable of remaining reliable through
      time. We will not penalize solutions that rely on reconnection to the mailserver upon reaching a bad state, but we
      will penalize solutions that cannot gracefully recover and continue executing calls.</p>
    <ul>
      <li>
        <p>This is a principle similar to ACID when working with databases; basically, if we queue up commands
          <code>C1</code>, <code>C2</code>, and <code>C3</code>, your solution should execute those in order.</p>
      </li>
      <li>
        <p>If <code>C2</code> reaches a passable error, your solution should attempt to gracefully recover (e.g.
          reconnect to mailserver) before trying <code>C2</code> and <code>C3</code> in order again.</p>
      </li>
      <li>
        <p>If the error is impassable, your solution should either stop and return an error if <code>C2</code> is a
          critical command (e.g. connection to mailserver) or return an error but continue with <code>C3</code>
          otherwise (e.g. if <code>C2</code> just fetches messages in a range).</p>
      </li>
      <li>
        <p>Tip: you will want to implement some sort of queue for incoming commands, so that they can be batched or
          replayed. The mailserver may angrily disconnect if you send too many commands or at random intervals, so being
          able to recover and replay is a key consideration. We recommend checking the connection before running every command!</p>
      </li>
    </ul>
  </li>
  <li>
    <p><strong>Interoperability</strong> with TypeScript</p>
    <ul>
      <li>
        <p>Our test suite is implemented in TypeScript and will utilize your solution either via binary (we'll compile
          the .NET code to build this) or via direct interop (e.g. WASM bindings for JavaScript if you used Rust).</p>
      </li>
      <li>
        <p>For this reason, please ensure you are exposing an API (hint hint: WebSockets are the best choice here!) that
          our engineers can configure the test suite to connect to.</p>
      </li>
      <li>
        <p>How easy/difficult the API is to use will factor into the score for interoperability.</p>
      </li>
    </ul>
  </li>
</ul>
<p></p>
<h2>"This spec is so long!"</h2>
<p>We promise it's not as overwhelming as it seems! For applicants, we are quite lenient on stability and design as you
  are only given two days. For vendors, we aren't very lenient but you've got a full week!</p>
<p>We recommend doing the exercise in small sprints, designing out your solution, and not worrying too much about the
  detail bullet points -- many of them are optional or provide information/hints to get you unstuck.</p>
<p>We know that this may seem daunting and gigantic for an introductory task for new hires, but most of our applicants
  so far have been able to finish it within a few hours. This task will also replace many of our interview rounds as it
  tests most of the skills and facets we're looking for! Moreover, it builds an important understanding of IMAP that
  will be vital once you join Aiko Mail.</p>
<p>Like we mentioned in the introduction, this is an exercise in implementing a subset of Post Office 2, which is an
  upgrade to the Post Office microservice, which is a small component of the Mouseion service, which is a component of
  just the frontend of Aiko Mail. You'll be dealing with tasks much more daunting than this on a daily basis at Aiko
  Mail, but like this, once you get acquainted you'll be able to identify an easy solution. By no means is this a target
  for this exercise, but in production this subset of Post Office 2 is a single 300 line script!</p>
<h1>Getting Started</h1>
<p>For the purpose of this task, you may utilize an <strong>open source .NET or Rust </strong>library to perform the
  relevant IMAP operations such as opening an inbox, fetching messages, etc. We personally recommend the
  <strong>Mailkit</strong> library (https://github.com/jstedfast/MailKit) for .NET and rust-imap for Rust. (But you may
  use any <strong>open source .NET or Rust library, preferably MIT or BSD licensed</strong>).</p>
<p>Please <strong>do not</strong>&nbsp;use a library written primarily in JS/TS, PHP, Python, or other language outside
  of C#/.NET and Rust as your solution will be benchmarked for speed, stability and cross-platform interop, and will be
  heavily marked down for using anything outside of <strong>.NET or Rust</strong>. Please also take care to ensure any
  tools or packages you utilize are open source, as our team cannot verify or mark your solution otherwise. <strong>We
    will not accept solutions utilizing closed source or proprietary software included within your source code, as while
    this may allow us to verify or mark your solution, we cannot do so due to IP regulations.</strong></p>
<p><em>Disclaimer: Solutions utilizing closed source or proprietary software will not be tested and immediately
    disqualified. Aiko Mail, Inc. does not accept any liability or responsibility for software you may send to us in
    electronic, paper, or other format that includes copyrighted, closed source, regulated or proprietary material.</em>
</p>
<h1>The Task 🦭</h1>
<p><strong>You will write a process that shall run as a Daemon (in the background on its own) and expose an API (ideally
    in the form of WebSockets) that returns content in JSON format (you may encrypt, compress, etc as you
    wish)</strong>. Your assessor will configure the test suite to connect to your API and simulate a number of commands
  on a test mailserver. Expected capabilities are outlined below.</p>
<p><strong>Note</strong>: IMAP connections are formed using a number of methods (the helper library you choose may
  support many different types). We look for <strong>two specific authentication methods: XOAuth2 and Basic
    Auth.</strong> You may assume at a minimum that we will supply the <em>host</em>, <em>port</em>, <em>whether to use
    TLS</em> (boolean), and <em>username</em>. In addition we will supply a <em>password</em> (for Basic Auth) or an
  <em>OAuth token</em> (for XOAUTH2).</p>
<p>Emails generally follow a certain structure (the form your library returns will be outlined in its documentation).
  You can assume the following:</p>
<ul>
  <li>
    <p>The mailbox supports the UID extension and every message within a folder has a monotonically increasing UID (but
      this is not consecutive). <em>e.g. if the mailserver receives Message 1, Message 2, and Message 3 in that order,
        one possible configuration could be (Message 1 --&gt; UID 14, Message 2 --&gt; UID 15, Message 3 --&gt; UID
        182).</em></p>
    <ul>
      <li>
        <p>Please choose a library that supports UID fetching (most do, both of our recs do!). That will make your life
          easy as you'll just have to call the relevant UID methods to meet this assumption.</p>
      </li>
      <li>
        <p>You may ignore UIDVALIDITY (our test suite will deal with this on its own).</p>
      </li>
    </ul>
  </li>
</ul>
<p>The original, open source Post Office library utilizes a TypeScript-based IMAP daemon to facilitate the following
  methods, which you are expected to re-implement in either .NET or Rust:</p>
<ul>
  <li>
    <p>Network methods, facilitating the connection to the mailserver which holds the emails.</p>
    <ul>
      <li>
        <p><strong>Connect to the mailserver.</strong> Return true on success.&nbsp;This should support, at the minimum,
          XOAuth2 and Basic Auth. You should not implement these as they already exist in the helper library you may use
          for this task (discussed later). You may assume that the process will only deal with one mailbox at a time. If
          this is called while the library is already connected, you should return an error message. You may assume this
          will include a full set of credentials required to connect to the mailserver (host, port, TLS, username,
          password/OAuth token, etc.).</p>
      </li>
      <li>
        <p><strong>Reconnect to the mailserver.</strong>&nbsp;Return true on success. This is similar to the connect
          method. This method may be called with a subset of the credentials required to connect to the mailserver (for
          example, a new host if the IMAP server/port has changed, or a new OAuth token upon expiry).</p>
        <ul>
          <li>
            <p>Tip: do not bother trying to negotiate the existing connection. You may simply close the connection and
              reopen it, using the existing credentials and updating them with any new credentials supplied to this
              method.</p>
          </li>
        </ul>
      </li>
      <li>
        <p><strong>Close connection to the mailserver.</strong>&nbsp;Return true on success. This should close out any
          TLS connections, garbage-collect anything allocated for the mailbox, and more or less reset the process.</p>
      </li>
      <li>
        <p><strong>Check connection.</strong>&nbsp;Returns a boolean. This should check the existing connection to the
          mailserver and verify that the wrapper either can or cannot manage emails. Please note that the connection may
          be alive and OK, but in an unauthenticated state, and in this situation this method should return false. Speed is key here as this should be called quite a bit to ensure stability.</p>
      </li>
    </ul>
  </li>
  <li>
    <p>Folder methods. Mailboxes organize emails into folders.</p>
    <ul>
      <li>
        <p>Get folders. Should return an array/list of the folders. Take care that a folder should be a nested structure
          (to represent folders within parents), and should serialize from your API like so:</p>
        <ul>
          <li>
            <p><code>delimiter</code> (string, e.g. for <code>[Gmail]/All Mail</code>, the delimiter is <code>/</code>.
              Most libraries will return this automatically</p>
          </li>
          <li>
            <p><code>path</code> (string) the full path, e.g. <code>[Gmail]</code> for the parent folder, then
              <code>[Gmail]/All Mail</code> for the child folder named <code>All Mail</code></p>
          </li>
          <li>
            <p><code>slug</code> (string) the partial path, e.g. <code>[Gmail]</code> for the parent folder, then
              <code>All Mail</code> for <code>[Gmail]/All Mail</code></p>
          </li>
          <li>
            <p><code>children</code> (array) an array of serialized Folder objects that are children of the current
              folder</p>
          </li>
          <li>
            <p><code>flags</code> (string array) the mailbox flags, e.g. <code>\Trash</code> <code>\Drafts</code>
              <code>\Sent</code></p>
          </li>
          <li>
            <p>you may return listed and subscribed attributes if you wish but these are optional and will
              <strong>not</strong> net you extra points</p>
          </li>
        </ul>
      </li>
      <li>
        <p>New folder. Should create a new folder given the path (e.g. <code>[Gmail]/Example</code>). You may assume the
          path given is the absolute path.</p>
      </li>
      <li>
        <p>Delete folder. Deletes a folder given the path. You may assume the path given is the absolute path.</p>
      </li>
      <li>
        <p>Open folder. Opens the folder on the mailserver. You may assume the path given is the absolute path.</p>
        <ul>
          <li>
            <p>Tip: choose a library that supports this out of the box, both MailKit and rust-imap do. This is a weird
              use case that involves leaving a mailbox open and listening for changes.</p>
          </li>
        </ul>
      </li>
    </ul>
  </li>
  <li>
    <p>Managing messages within the mailserver.</p>
    <ul>
      <li>
        <p>Fetch messages, a comprehensive method to fetch messages given a configuration.</p>
        <ul>
          <li>
            <p>At minimum, will provide a path (the folder to fetch emails from) and a sequence (string, UID sequence,
              e.g. <code>123</code> for #123 or <code>4:40</code> for messages #4 thru #40).</p>
            <ul>
              <li>
                <p>You should throw an error if the path is empty or the sequence starts with 0.</p>
              </li>
            </ul>
          </li>
          <li>
            <p>May provide the following options:</p>
            <ul>
              <li>
                <p><strong>flag</strong> (boolean, default true): whether to return flag information such as Seen,
                  Starred, Draft. This is also the minimum level of info returned by this call.</p>
              </li>
              <li>
                <p><strong>envelope</strong> (boolean, default true): whether to return envelope information such as
                  recipients, subject line, etc.</p>
              </li>
              <li>
                <p><strong>headers</strong> (boolean, default false): whether to return message headers such as Message
                  ID</p>
              </li>
              <li>
                <p><strong>bodystructure</strong> (boolean, default false): <strong>optional</strong> whether to return
                  the BODYSTRUCTURE information detailing MIME parts. You might need this for certain libraries, but we
                  don't really need this.</p>
              </li>
              <li>
                <p><strong>content</strong> (boolean, default false): whether to return the text and HTML content of an
                  email (MIME-parsed! not the raw!)</p>
              </li>
              <li>
                <p><strong>attachments</strong> (boolean, default false): whether to return the attachments (MIME-parsed
                  generally, except if CID is set to true explicitly)</p>
              </li>
              <li>
                <p><strong>parse</strong> (boolean, default true): <strong>optional</strong> this will usually be set to
                  true, but a single extra point will be awarded to solutions that do not MIME parse content or
                  attachments <strong>if </strong>this is set to false</p>
              </li>
              <li>
                <p><strong>markAsSeen</strong> (boolean, default false): whether to mark the messages as seen
                  (<code>body.peek</code> vs <code>body</code>), note that many libraries might default this to
                  true/false</p>
              </li>
              <li>
                <p><strong>modseq</strong> (string or number, you pick): <strong>optional</strong> if you choose to
                  implement the CONDSTORE extension for extra points, this will be provided to facilitate that</p>
              </li>
              <li>
                <p><strong>cids</strong> (boolean): if set to true, don't parse attachments and leave inline attachments
                  as the CIDs (just not parsing attachments usually is enough for this)</p>
              </li>
              <li>
                <p><strong>limit</strong> (number): the maximum number of messages to return. Tip: since UIDs are not
                  consecutive, do not limit the initial fetching of messages. Instead, limit between fetching and
                  parsing.</p>
              </li>
            </ul>
          </li>
          <li>
            <p>if any of the above are null or not provided, assume their defaults</p>
          </li>
          <li>
            <p>some libraries automatically convert text to HTML and HTML to text if the complement is missing; this is
              not required and actually not preferred for speed so you may skip this</p>
          </li>
          <li>
            <p>Tip: this is the most important method in the spec! Spend the most time ensuring this works and is as
              optimized/efficient as possible as this is the largest bottleneck in the test suite by far.</p>
          </li>
        </ul>
      </li>
      <li>
        <p>Several methods that are simply helper functions to the list messages method (we're looking to test your
          design skills here)</p>
        <ul>
          <li>
            <p>Fetch Messages with flags (provided <strong>path</strong> and <strong>sequence</strong>, assume
              <strong>flags</strong>=true, <strong>envelope</strong>=false, should take in <strong>modseq</strong> and
              <strong>limit</strong> as additional options)</p>
          </li>
          <li>
            <p>Fetch Messages with envelopes (provided <strong>path</strong> and <strong>sequence</strong>, assume
              <strong>flags</strong>=true, <strong>envelope</strong>=true, should take in <strong>modseq</strong> and
              <strong>limit</strong> as additional options)</p>
          </li>
          <li>
            <p>Fetch Messages with headers (provided path and sequence, assume <strong>flags</strong>=true,
              <strong>envelope</strong>=true, <strong>headers</strong>=true, should take in <strong>modseq</strong>,
              <strong>limit</strong>, <strong>markAsSeen</strong>, and <strong>parse</strong> as additional options)</p>
          </li>
          <li>
            <p>Fetch Messages in full (provided <strong>path</strong> and <strong>sequence</strong>, assume
              <strong>flags</strong>=true, <strong>envelope</strong>=false, <strong>headers</strong>=true,
              <strong>content</strong>=true, should take in <strong>modseq</strong>, <strong>limit, bodystructure,
                parse, markAsSeen, attachments, </strong>and<strong> cids</strong> as additional options)</p>
          </li>
        </ul>
      </li>
      <li>
        <p>Search Messages. Should at minimum support string queries (extra points given for better designs with more
          options e.g. subject searching, sender searching, message ID or other header searching, date filters, etc)</p>
        <ul>
          <li>
            <p>By default, some libraries have IMAP UID search which returns a list of UIDs. In this case, it's your
              responsibility to take that list and fetch the relevant messages.</p>
          </li>
          <li>
            <p>Given a path and query string at minimum</p>
          </li>
          <li>
            <p><strong>Optional: </strong>implement a <code>fetch</code> option for this which is true by default, but
              if explicitly set to false, do not fetch the relevant messages and simply return UIDs. 1 extra point.</p>
          </li>
        </ul>
      </li>
      <li>
        <p>Delete Messages. Given a path and UID sequence, delete the given messages from the mailserver. Return true on
          success.</p>
      </li>
      <li>
        <p>Flag Messages. Given a path, UID sequence, and flag options, apply the flag options to the given messages on
          the mailserver. Return true on success.</p>
        <ul>
          <li>
            <p>Flag options are typed as an array of strings (e.g. <code>["\Seen", "\Starred"]</code>)</p>
            <ul>
              <li>
                <p><strong>set</strong> (array of strings): if this is set, clear all flags then set only these</p>
                <ul>
                  <li>
                    <p>assume that set will not be provided in conjunction with add and remove</p>
                  </li>
                </ul>
              </li>
              <li>
                <p><strong>add </strong>(array of strings): add these flags</p>
              </li>
              <li>
                <p><strong>remove</strong> (array of strings): remove these flags</p>
              </li>
            </ul>
          </li>
        </ul>
      </li>
      <li>
        <p>Copy Messages. Given a source folder path, UID sequence, and destination folder path, <strong>copy</strong>
          the messages from the source folder to the destination folder. Return the new UIDs on success.</p>
      </li>
      <li>
        <p>Move Messages. Given a source folder path, UID sequence, and destination folder path, <strong>move</strong>
          the messages from the source folder to the destination folder. Return the new UIDs on success.</p>
      </li>
      <li>
        <p>Upload Message. <strong>Optional</strong>, used to upload a message to a folder, for example to the Drafts
          folder. Extra points if implemented.</p>
      </li>
    </ul>
  </li>
</ul>

## Key Considerations

Please note that the following are key considerations for the implementation of the spec:
- The API is JSON based. This means that the client and server **must** be able to communicate with each other in a JSON-based format!
- The API is stateful. This means that the process must preserve state, e.g. it should handle one mailbox at a time and methods may be delivered in sequence over time (e.g. connect, list folders, fetch messages, wait 5 min, list folders, fetch messages, wait 3 min, move messages, etc)
- The API is meant to run locally on a device. It should not require complex build steps, containerization or etc., instead opt for a simple, local implementation that compiles to an executable binary.
- The API should be cross-platform. Solutions that rely on native methods and cannot be cross-compiled to Windows and Mac at a minimum will be marked down. The test suite will run on both Windows and Mac, and the point breakdown between the two is approximately 50/50 (slightly more points will be awarded to Mac implementations, as applicants have met some difficulty in the past with M1 chips and .NET)