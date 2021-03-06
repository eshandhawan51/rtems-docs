.. SPDX-License-Identifier: CC-BY-SA-4.0

.. Copyright (C) 2019 embedded brains GmbH

.. |E40| replace:: ECSS-E-ST-40C

.. |E10-06| replace:: ECSS-E-ST-10-06C

.. _ReqEng:

Software Requirements Engineering
*********************************

Software engineering standards for critical software such as |E40| demand that
software requirements for a software product are collected in a software
requirements specification (technical specification in |E40| terms).  They are
usually derived from system requirements (requirements baseline in |E40|
terms).  RTEMS is designed as a reusable software product which can be utilized
by application designers to ease the development of their applications.  The
requirements of the end system (system requirements) using RTEMS are only known
to the application designer.  RTEMS itself is developed by the RTEMS
maintainers and they do not know the requirements of a particular end system in
general.  RTEMS is designed as a real-time operating system to meet typical
system requirements for a wide range of applications.  Its suitability for a
particular application must be determined by the application designer based on
the technical specification provided by RTEMS accompanied with performance data
for a particular target platform.

Currently, no technical specification of RTEMS exists in the form of a
dedicated document.  Since the beginning of the RTEMS evolution in the late
1980s it was developed iteratively.  It was never developed in a waterfall
model.  During initial development the RTEID :cite:`Motorola:1988:RTEID` and
later the ORKID :cite:`VITA:1990:ORKID` draft specifications were used as
requirements.  These were evolving during the development and an iterative
approach was followed often using simple algorithms and coming back to
optimise.  In 1993 and 1994 a subset of pthreads sufficient to support
:term:`GNAT` was added as requirements.  At this time the Ada tasking was
defined, however, not implemented in GNAT, so this involved guessing during the
development. Later some adjustments were made when Ada tasking was actually
implemented.  So, it was consciously iterative with the specifications evolving
and feedback from performance analysis.  Benchmarks published from other real
time operating systems were used for comparison.  Optimizations were carried
out until the results were comparable.  Development was done with distinct
contractual phases and tasks for development, optimization, and the addition of
priority inheritance and rate monotonic scheduling.  The pthreads requirement
has grown to be as much POSIX as possible.

Portability from FreeBSD to use its network stack, USB stack, SD/MMC card stack
and device drivers resulted in another set of requirements.  The addition of
support for symmetric multiprocessing (SMP) was a huge driver for change.  It
was developed step by step and sponsored by several independent users with
completely different applications and target platforms in mind.  The high
performance OpenMP support introduced the Futex as a new synchronization
primitive.

.. topic:: Guidance

    A key success element of RTEMS is the ability to accept changes driven by
    user needs and still keep the operating system stable enough for production
    systems.  Procedures that place a high burden on changes are doomed to be
    discarded by the RTEMS Project.  We have to keep this in mind when we
    introduce a requirements management work flow which should be followed by
    RTEMS community members and new contributors.

We have to put in some effort first into the reconstruction of software
requirements through reverse engineering using the RTEMS documentation, test
cases, sources, standard references, mailing list archives, etc. as input.
Writing a technical specification for the complete RTEMS code base is probably
a job of several person-years.  We have to get started with a moderate feature
set (e.g. subset of the Classic API) and extend it based on user demands step
by step.

The development of the technical specification will take place in two phases.
The first phase tries to establish an initial technical specification for an
initial feature set.  This technical specification will be integrated into
RTEMS as a big chunk.  In the second phase the technical specification is
modified through arranged procedures.  There will be procedures

* to modify existing requirements,

* add new requirements, and

* mark requirements as obsolete.

All procedures should be based on a peer review principles.

Requirements for Requirements
=============================

.. _ReqEngIdent:

Identification
--------------

Each requirement shall have a unique identifier (UID).  The question is in
which scope should it be unique?  Ideally, it should be universally unique. As
a best effort approach, the name *RTEMS* shall be used as a part in all
requirement identifiers. This should ensure that the RTEMS requirements can be
referenced easily in larger systems.  The standard ECSS-E-ST-10-06C recommends
in section 8.2.6 that the identifier should reflect the type of the requirement
and the life profile situation.  Other standards may have other
recommendations.  To avoid a bias of RTEMS in the direction of ECSS, this
recommendation will not be followed.

.. topic:: Doorstop

    The UID of an item (requirement) is defined by its file name without the
    extension. An UID consists of two parts, the prefix and a name or a number.
    The parts are divided by an optional separator. The prefix is determined by
    the document.

The UID scheme for RTEMS requirements is the concatenation of *RTEMS*, one or
more component names, and a name.  Each part is separated by a "-"
character.  For example, the UID RTEMS-CLASSIC-TASK-CREATERRINVADDR may specify
that the `rtems_task_create()` directive shall return a status of
`RTEMS_INVALID_ADDRESS` if the `id` parameter is `NULL`.

.. topic:: Doorstop

    Doorstop uses documents to create namespaces (named a prefix in Doorstop).
    For the example above, you can create the documents like this:

    .. code-block:: none

        doorstop create -s - RTEMS-CLASSIC -p RTEMS spec/classic
        doorstop create -s - RTEMS-CLASSIC-TASK -p RTEMS-CLASSIC spec/classic/task
        doorstop create -s - RTEMS-CLASSIC-SEMAPHORE -p RTEMS-CLASSIC spec/classic/semaphore

    The requirement files are organized in directories.

A initial requirement item hierarchy could be this:

* RTEMS

  * BUILD (building RTEMS BSPs and libraries)

  * CONFIG (application configuration)

  * CLASSIC

    * TASK

      * CREAT* (requirements for `rtems_task_create()`)
      * DELT* (requirements for `rtems_task_delete()`)
      * EXIT* (requirements for `rtems_task_exit()`)
      * GETAFF* (requirements for `rtems_task_get_affinity()`)
      * GETPRI* (requirements for `rtems_task_get_priority()`)
      * GETSHD* (requirements for `rtems_task_get_scheduler()`)
      * IDNT* (requirements for `rtems_task_ident()`)
      * ISSUSP* (requirements for `rtems_task_is_suspended()`)
      * ITER* (requirements for `rtems_task_iterate()`)
      * MODE* (requirements for `rtems_task_mode()`)
      * RESTRT* (requirements for `rtems_task_restart()`)
      * RESUME* (requirements for `rtems_task_resume()`)
      * SELF* (requirements for `rtems_task_self()`)
      * SETAFF* (requirements for `rtems_task_set_affinity()`)
      * SETPRI* (requirements for `rtems_task_set_priority()`)
      * SETSHD* (requirements for `rtems_task_set_scheduler()`)
      * START* (requirements for `rtems_task_start()`)
      * SUSP* (requirements for `rtems_task_suspend()`)
      * WKAFT* (requirements for `rtems_task_wake_after()`)
      * WKWHN* (requirements for `rtems_task_wake_when()`)

    * Semaphore

      * ...

  * POSIX

  * ...

A more detailed naming scheme and guidelines should be established.  We have to
find the right balance between the length of UIDs and self-descriptive UIDs.  A
clear scheme for all Classic API managers may help to keep the UIDs short and
descriptive.

The specification of the validation of requirements should be maintained also by
Doorstop.  For each requirement document there should be a validation child
Doorstop document with a *TEST* component name, e.g. RTEMS-CLASSIC-TASK-TEST.  A
test document may contain also validations by analysis, by inspection, and by
design, see :ref:`ReqEngValidation`.

Level of Requirements
---------------------

The level of a requirement shall be expressed with one of the verbal forms
listed below and nothing else.  The level of requirements are derived from RFC
2119 :cite:`RFC2119` and |E10-06| :cite:`ECSS_E_ST_10_06C`.

Absolute Requirements
~~~~~~~~~~~~~~~~~~~~~

Absolute requirements shall be expressed with the verbal form *shall* and no
other terms.

Absolute Prohibitions
~~~~~~~~~~~~~~~~~~~~~

Absolute prohibitions shall be expressed with the verbal form *shall not* and
no other terms.

.. warning::

    Absolute prohibitions may be difficult to validate.  They should not be
    used.

Recommendations
~~~~~~~~~~~~~~~

Recommendations shall be expressed with the verbal forms *should* and
*should not* and no other terms with guidance from RFC 2119:

    SHOULD   This word, or the adjective "RECOMMENDED", mean that there
    may exist valid reasons in particular circumstances to ignore a
    particular item, but the full implications must be understood and
    carefully weighed before choosing a different course.

    SHOULD NOT   This phrase, or the phrase "NOT RECOMMENDED" mean that
    there may exist valid reasons in particular circumstances when the
    particular behavior is acceptable or even useful, but the full
    implications should be understood and the case carefully weighed
    before implementing any behavior described with this label.

Permissions
~~~~~~~~~~~

Permissions shall be expressed with the verbal form *may* and no other terms
with guidance from RFC 2119:

    MAY   This word, or the adjective "OPTIONAL", mean that an item is
    truly optional.  One vendor may choose to include the item because a
    particular marketplace requires it or because the vendor feels that
    it enhances the product while another vendor may omit the same item.
    An implementation which does not include a particular option MUST be
    prepared to interoperate with another implementation which does
    include the option, though perhaps with reduced functionality. In the
    same vein an implementation which does include a particular option
    MUST be prepared to interoperate with another implementation which
    does not include the option (except, of course, for the feature the
    option provides.)

Possibilities and Capabilities
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Possibilities and capabilities shall be expressed with the verbal form *can*
and no other terms.

.. _ReqEngSyntax:

Syntax
------

Use the Easy Approach to Requirements Syntax (:term:`EARS`) to formulate
requirements.  A recommended reading list to get familiar with this approach is
:cite:`Mavin:2009:EARS`, :cite:`Mavin:2010:BigEars`, and
:cite:`Mavin:2016:LLEARS`.  Please also have a look at the EARS quick reference
sheet :cite:`Uusitalo:2012:EARS`.  The sentence types are:

* Ubiquitous

    The <system name> shall <system response>.

* Event-driven

    *When* <optional preconditions> <trigger>, the <system name> shall <system response>.

* State-driven

    *While* <in state>, the <system name> shall <system response>.

* Unwanted behaviour

    *If* <optional preconditions> <trigger>, *then* the <system name> shall <system response>.

* Optional

    *Where* <feature>, the <system name> shall <system response>.

The optional sentence type should be only used for application configuration
options.  The goal is to use the *enabled-by* attribute to enable or disable
requirements based on configuration parameters that define the RTEMS artefacts
used to build an application executable (header files, libraries, linker command
files).  Such configuration parameters are for example the architecture, the
platform, CPU port options, and build configuration options (e.g. uniprocessor
vs. SMP).

Wording Restrictions
--------------------

To prevent the expression of imprecise requirements, the following terms shall
not be used in requirement formulations:

* "acceptable"
* "adequate"
* "almost always"
* "and/or"
* "appropriate"
* "approximately"
* "as far as possible"
* "as much as practicable"
* "best"
* "best possible"
* "easy"
* "efficient"
* "e.g."
* "enable"
* "enough"
* "etc."
* "few"
* "first rate"
* "flexible"
* "generally"
* "goal"
* "graceful"
* "great"
* "greatest"
* "ideally"
* "i.e."
* "if possible"
* "in most cases"
* "large"
* "many"
* "maximize"
* "minimize"
* "most"
* "multiple"
* "necessary"
* "numerous"
* "optimize"
* "ought to"
* "probably"
* "quick"
* "rapid"
* "reasonably"
* "relevant"
* "robust"
* "satisfactory"
* "several"
* "shall be included but not limited to"
* "simple"
* "small"
* "some"
* "state-of-the-art".
* "sufficient"
* "suitable"
* "support"
* "systematically"
* "transparent"
* "typical"
* "user-friendly"
* "usually"
* "versatile"
* "when necessary"

For guidelines to avoid these terms see Table 11-2, "Some ambiguous terms to
avoid in requirements" in :cite:`Wiegers:2013:SR`.  There should be some means
to enforce that these terms are not used, e.g. through a client-side pre-commit
Git hook, a server-side pre-receive Git hook, or some scripts run by special
build commands.

Separate Requirements
---------------------

Requirements shall be stated separately.  A bad example is:

RTEMS-CLASSIC-TASK-CRAT
    The task create directive shall evaluate the parameters, allocate a task
    object and initialize it.

To make this a better example, it should be split into separate requirements:

RTEMS-CLASSIC-TASK-CRAT
    When the task create directive is called with valid parameters and a free
    task object exists, the task create directive shall assign the identifier of
    an initialized task object to the id parameter and return the
    RTEMS_SUCCESSFUL status.

RTEMS-CLASSIC-TASK-CRATERRTOOMANY
    If no free task objects exists, the task create directive shall return the
    RTEMS_TOO_MANY status.

RTEMS-CLASSIC-TASK-CRATERRINVADDR
    If the id parameter is NULL, the task create directive shall return the
    RTEMS_INVALID_ADDRESS status.

RTEMS-CLASSIC-TASK-CRATERRINVNAME
    If the name parameter is not valid, the task create directive shall return
    the RTEMS_INVALID_NAME status.

    ...

Conflict Free Requirements
--------------------------

Requirements shall not be in conflict with each other inside a specification.
A bad example is:

RTEMS-CLASSIC-SEMAPHORE-MTXOBWAIT
    If a mutex is not available, the mutex obtain directive shall enqueue the
    calling thread on the wait queue of the mutex.

RTEMS-CLASSIC-SEMAPHORE-MTXOBERRUNSAT
    If a mutex is not available, the mutex obtain directive shall return the
    RTEMS_UNSATISFIED status.

To resolve this conflict, a condition may be added:

RTEMS-CLASSIC-SEMAPHORE-MTXOBWAIT
    If a mutex is not available, when the RTEMS_WAIT option is set, the mutex
    obtain directive shall enqueue the calling thread on the wait queue of the
    mutex.

RTEMS-CLASSIC-SEMAPHORE-MTXOBERRUNSAT
    If a mutex is not available, when the RTEMS_WAIT option is not set, the
    mutex obtain directive shall return the RTEMS_UNSATISFIED status.

Use of Project-Specific Terms and Abbreviations
-----------------------------------------------

All project-specific terms and abbreviations used to formulate requirements
shall be defined in the project glossary.

.. _ReqEngJustReq:

Justification of Requirements
-----------------------------

Each requirement shall have a rationale or justification recorded in a
dedicated section of the requirement file.

.. topic:: Doorstop

    See *rationale* attribute for :ref:`ReqEngSpecItems`.

.. _ReqEngSpecItems:

Specification Items
===================

The technical specification of RTEMS will contain requirements, specializations
of requirements, :ref:`test procedures <ReqEngTestProcedure>`,
:ref:`test suites <ReqEngTestSuite>`, :ref:`test cases <ReqEngTestCase>`, and
:ref:`requirement validations <ReqEngValidation>`.  These things will be called
*specification items* or just *items* if it is clear from the context.

.. topic:: Doorstop

    Doorstop maintains *items* which are included in *documents*.  The normal
    use case is to store a requirement with meta-data in an item.  However,
    items can be also used to store other things like test procedures, test
    suites, test cases, and requirement validations.  Items contain key-value
    pairs called attributes.  Specializations of requirements may contain extra
    attributes, e.g. interface, build, configuration requirements. All items
    have the following standard Doorstop attributes:

    active
        A boolean value which indicates if the requirement is active or not.
        The value is included in the fingerprint via a document configuration
        option.

    derived
        A boolean value which indicates if the requirement is derived or not.
        For the
        `definition of derived <https://github.com/jacebrowning/doorstop/blob/develop/docs/reference/item.md#derived>`_.
        see the Doorstop documentation.  For RTEMS, this value shall be false
        for all requirements.  The value is not included in the fingerprint.

    normative
        A boolean value which indicates if the requirement is normative or not.
        For the
        `definition of normative <https://github.com/jacebrowning/doorstop/blob/develop/docs/reference/item.md#normative>`_.
        see the Doorstop documentation.  For RTEMS, this value shall be true
        for all requirements.  The value is not included in the fingerprint.

    level
        Indicates the presentation order within a document.  For RTEMS, this
        value shall be unused.  The value is not included in the fingerprint.

    header
        A header for an item.  For RTEMS, this value shall be the empty string.
        The value is not included in the fingerprint.

    reviewed
        The fingerprint of the item.  Maintain this attribute with the
        `doorstop clear` command.

    links
        The links from this item to parent items.  Maintain this attribute with
        the `doorstop link` command.  The value is included in the fingerprint.

    ref
        References to files and directories. See
        `#365 <https://github.com/jacebrowning/doorstop/issues/365>`_,
        The value is included in the fingerprint.

    text
        The item text.  The value is included in the fingerprint.

    All specification items shall have the following extended attributes:

    type:
        A list of :ref:`item types <ReqEngItemTypes>`.  The value is not
        included in the fingerprint.

    enabled-by:
        The value is a list of expressions.  The value is included in the
        fingerprint.  An expression is an operator or an option.  An option
        evaluates to true if it is included the set of enabled options of  the
        configuration.  An operator is a dictionary with exactly one key and a
        value.  Valid keys are `and`, `or`, and `not`:

        * The value of the `and` operator shall be a list of expressions.  It
          evaluates to the `logical and` of all outcomes of the expressions in
          the list.

        * The value of the `or` operator shall be a list of expressions.  It
          evaluates to the `logical or` of all outcomes of the expressions in
          the list.

        * The value of the `not` operator shall be an expression.  It negates
          the outcome of its expression.

        The outcome of a list of expressions without an operator is the
        `logical or` of all outcomes of the expressions in the list.  An empty
        list evaluates to true.  Examples:

        .. code-block:: none

            enabled-by:
            - RTEMS_SMP

        .. code-block:: none

            enabled-by:
            - and:
              - RTEMS_NETWORKING
              - not: RTEMS_SMP

        .. code-block:: none

            enabled-by:
            - and:
              - not: TEST_DEBUGGER01_EXCLUDE
              - or:
                - arm
                - i386

.. _ReqEngItemTypes:

Item Types
----------

Specification items can have all sorts of *types*.  The selection of types and
the level of detail depends on a particular standard and product model.  We need
enough flexibility to be in line with ECSS-E-ST-10-06 and possible future
applications of other standards.  Each item may have a list of types.  Valid
types are listed below.  This list may change over time.  If new types are
added, then a mapping between types should be specified.  The item types and
their definition is work in progress.  A list of types follows:

* requirement

    * functional - Functional requirements shall describe the behaviour of the
      software product under specific conditions.

        * *capability*

        * *dependability-function*

        * *function*

        * *operational* - Operational requirements shall

            * define the operation modes (e.g. initialization, multitasking, termination),

            * describe the operation modes, and

            * describe the operation mode transitions.

        * *safety-function*

    * non-functional

        * *build-configuration*

        * *constraint*

        * *design*

        * *interface*

        * *interface-requirement*

        * *maintainability*

        * *performance*

        * *portability*

        * *quality*

        * *reliability*

        * *resource*

        * *safety*

* *test-procedure*

* *test-suite*

* *test-case*

* *validation-by-analysis*

* *validation-by-inspection*

* *validation-by-review-of-design*

* *validation-platform*

.. image:: ../images/eng/req-spec-items.*
    :scale: 70
    :align: center

Requirements
------------

.. topic:: Doorstop

    All requirement specification items shall have the following extended
    attribute:

    rationale:
        The rationale or justification of the specification item.  The value is
        **not** included in the fingerprint.

Build Configuration
-------------------

Build configuration requirements define what needs to be built (libraries,
object files, test executables, etc.) and how (configuration option header
files, compiler flags, linker flags, etc.).  The goal is to generate build
files (Makefile or waf) and content for the Software Configuration File (SCF)
from it.  A YAML scheme needs to be defined for this purpose.

.. _ReqEngInterfaceReq:

Interface Requirement
---------------------

Interface requirements shall describe the high level aspects of interfaces.
The item type shall be *interface-requirement*.

.. _ReqEngInterface:

Interface
---------

Interface items shall specify the interface of the software product to other
software products and the hardware.  The item type shall be *interface*.  The
interface items shall have an *interface-category* which is one of the
following and nothing else:

* *application*: Application interface items shall describe the interface
  between the software product and the application (:term:`API`).  The goal is
  to generate header files with Doxygen markup and user manual documentation
  parts from the application interface specification.

* *application-configuration*: Application configuration items shall define
  parameters of the software product which can be set by the application at
  link-time.  The goal is to generate user manual documentation parts and test
  cases from the application configuration specification.

* *architecture*: Architecture interface items shall define the
  interface between the software product and the processor architecture
  (:term:`ABI`).

* *gcc*: GCC interface items shall define the interface between the software
  product and GCC components such as libgcc.a, libatomic.a, libgomp.a,
  libstdc++.a, etc.

* *hardware*: Hardware interface items shall define the interface between the
  software product and the hardware.

* *newlib*: Newlib interface items shall define the interface between the
  software product and the Newlib (libc.a).

The interface items shall have an *interface-type* which is one of the
following and nothing else:

* *configuration-option*

* *define*

* *enum*

* *function*

* *header*

* *macro*

* *register-block*

* *structure*

* *typedef*

* *union*

* *variable*

.. _ReqEngInterfaceApplicationConfig:

Interface - Application Configuration
-------------------------------------

.. topic:: Doorstop

    The application configuration items shall have the following attribute
    specializations:

    type
        The type value shall be *interface*.

    interface-category
        The interface category value shall be *application-configuration*.

    interface-type
        The interface type value shall be *configuration-option*.

    link
        There shall be a link to a higher level requirement.

    text
        The application configuration requirement.

    configuration-category:
        A category to which this application configuration option belongs.

    define:
        The name of the configuration define.

    data-type:
        The data type of the configuration define.

    value-range:
        The range of valid values.

    default-value:
        The default value.

    description:
        The description of the application configuration.  The description
        should focus on the average use-case.  It should not cover potential
        problems, constraints, obscure use-cases, corner cases and everything
        which makes matters complicated.

    note:
        Notes for this application configuration.  The notes should explain
        everything which was omitted from the description.  It should cover all
        the details.

.. _ReqEngTestProcedure:

Test Procedure
--------------

Test procedures shall be executed by the Qualification Toolchain.

.. topic:: Doorstop

    The test procedure items shall have the following attribute
    specializations:

    type
        The type value shall be *test-procedure*.

    text
        The purpose of this test procedure.

    platform
        There shall be links to validation platform requirements.

    steps
        The test procedure steps.  Could be a list of key-value pairs.  The key
        is the step name and the value is a description of the actions
        performed in this step.

.. _ReqEngTestSuite:

Test Suite
----------

Test suites shall use the :ref:`RTEMS Test Framework <RTEMSTestFramework>`.

.. topic:: Doorstop

    The test suite items shall have the following attribute specializations:

    type
        The type value shall be *test-suite*.

    text
        The test suite description.

.. _ReqEngTestCase:

Test Case
---------

Test cases shall use the :ref:`RTEMS Test Framework <RTEMSTestFramework>`.

.. topic:: Doorstop

    The test case items shall have the following attribute specializations:

    type
        The type value shall be *test-case*.

    link
        The link to the requirement validated by this test case or no links if
        this is a unit or integration test case.

    ref
        If this is a unit test case, then a reference to the :term:`software
        item` under test shall be provided.  If this is an integration test
        case, then a reference to the integration under test shall be provided.
        The integration is identified by its Doxygen group name.

    text
        A short description of the test case.

    inputs
        The inputs to execute the test case.

    outputs
        The expected outputs.

    The test case code may be also contained in the test case specification
    item in a *code* attribute.  This is subject to discussion on the RTEMS
    mailing list.  Alternatively, the test code could be placed directly in
    source files.  A method is required to find the test case specification of
    a test case code and vice versa.

.. _ReqEngResAndPerf:

Resources and Performance
-------------------------

Normally, resource and performance requirements are formulated like this:

* The resource U shall need less than V storage units.

* The operation Y shall complete within X time units.

Such statements are difficult to make for a software product like RTEMS which
runs on many different target platforms in various configurations.  So, the
performance requirements of RTEMS shall be stated in terms of benchmarks.  The
benchmarks are run on the project-specific target platform and configuration.
The results obtained by the benchmark runs are reported in a human readable
presentation.  The application designer can then use the benchmark results to
determine if its system performance requirements are met.  The benchmarks shall
be executed under different environment conditions, e.g. varying cache states
(dirty, empty, valid) and system bus load generated by other processors.  The
application designer shall have the ability to add additional environment
conditions, e.g. system bus load by DMA engines or different system bus
arbitration schemes.

To catch resource and performance regressions via test suite runs there shall be
a means to specify threshold values for the measured quantities.  The threshold
values should be provided for each validation platform.  How this can be done
and if the threshold values are maintained by the RTEMS Project is subject to
discussion.

.. _ReqEngTrace:

Traceability of Specification Items
===================================

The standard |E10-06| demands that requirements shall be under configuration
management, backwards-traceable and forward-traceable.  Requirements are a
specialization of specification items in RTEMS.

.. _ReqEngTraceHistory:

History of Specification Items
------------------------------

The RTEMS specification items should placed in the RTEMS sources using Git for
version control.  The history of specification items can be traced with Git.
Special commit procedures for changes in specification item files should be
established.  For example, it should be allowed to change only one
specification item per commit.  A dedicated Git commit message format may be
used as well, e.g. use of ``Approved-by:`` or ``Reviewed-by:`` lines which
indicate an agreed statement (similar to the
`Linux kernel patch submission guidelines <https://www.kernel.org/doc/html/latest//process/submitting-patches.html#using-reported-by-tested-by-reviewed-by-suggested-by-and-fixes>`_).
Git commit procedures may be ensured through a server-side pre-receive hook.
The history of requirements may be also added to the specification items
directly in a ``revision`` attribute.  This would make it possible to generate
the history information for documents without having the Git repository
available, e.g. from an RTEMS source release archive.

.. _ReqEngTraceBackward:

Backward Traceability of Specification Items
--------------------------------------------

Providing backward traceability of specification items means that we must be
able to find the corresponding higher level specification item for each refined
specification item.  This is a standard Doorstop feature.

.. _ReqEngTraceForward:

Forward Traceability of Specification Items
-------------------------------------------

Providing forward traceability of specification items means that we must be
able to find all the refined specification items for each higher level
specification item.  This is a standard Doorstop feature.  The links from
parent to child specification items are implicitly defined by links from a
child item to a parent item.

.. _ReqEngTraceReqArchDesign:

Traceability between Software Requirements, Architecture and Design
-------------------------------------------------------------------

The software requirements are implemented in Doorstop compatible YAML files.
The software architecture and design is written in Doxygen markup.  Doxygen
markup is used throughout all header and source files.  A Doxygen filter
program may be provided to place Doxygen markup in assembler files.  The
software architecture is documented via Doxygen groups.  Each Doxygen group
name should have a project-specific name and the name should be unique within
the project, e.g.  RTEMSTopLevel\ MidLevel\ LowLevel.  The link from a Doxygen
group to its parent group is realized through the `@ingroup` special command.
The link from a Doxygen group or software component to the corresponding
requirement is realized through a `@satisfy{req}`
`custom command <http://www.doxygen.nl/manual/custcmd.html>`_
which needs the identifier of the requirement as its one and only parameter.
Only links to parents are explicitly given in the Doxygen markup.  The links
from a parent to its children are only implicitly specified via the link from a
child to its parent.  So, a tool must process all files to get the complete
hierarchy of software requirements, architecture and design. Links from a
software component to another software component are realized through automatic
Doxygen references or the ``@ref`` and ``@see`` special commands.

.. _ReqEngValidation:

Requirement Validation
======================

The validation of each requirement shall be accomplished by one or more of
the following methods and nothing else:

* *By test*: A :ref:`ReqEngTestCase` specification item is provided to
  demonstrate that the requirement is satisfied when the software product is
  executed on the target platform.

* *By analysis*: A statement is provided how the requirement is met, by
  analysing static properties of the software product.

* *By inspection*: A statement is provided how the requirement is met, by
  inspection of the :term:`source code`.

* *By review of design*: A rationale is provided to demonstrate how the
  qualification requirement is satisfied implicitly by the software design.

Validation by test is strongly recommended.  The choice of any other validation
method shall be strongly justified.  The requirements author is obligated to
provide the means to validate the requirement with detailed instructions.

.. topic:: Doorstop

    For an item in a parent document it is checked that at least one item in a
    child document has a link to it.  For example a child document could
    contain validation items.  With this feature you can check that all
    requirements are covered by at least one validation item.

    The requirement validation by analysis, by inspection, and by design
    specification items shall have the following attribute specializations:

    type
        The type attribute value shall be *validation-by-analysis*,
        *validation-by-inspection*, or *validation-by-review-of-design*.

    link
        There shall be exactly one link to the validated requirement.

    text
        The statement or rational of the requirement validation.

Requirement Management
======================

Change Control Board
--------------------

Working with requirements usually involves a Change Control Board
(:term:`CCB`).  The CCB of the RTEMS Project is the
`RTEMS developer mailing list <https://lists.rtems.org/mailman/listinfo/devel>`_.

There are the following actors involved:

* *RTEMS users*: Everyone using the RTEMS real-time operating system to design,
  develop and build an application on top of it.

* *RTEMS developers*: The persons developing and maintaining RTEMS.  They write
  patches to add or modify code, requirements, tests and documentation.

* *RTEMS maintainers*: They are listed in the
  `MAINTAINERS <https://git.rtems.org/rtems/tree/MAINTAINERS>`_ file and have
  write access to the project repositories.

Adding and changing requirements follows the normal patch review process.  The
normal patch review process is described in the
`RTEMS User Manual <https://docs.rtems.org/branches/master/user/support/contrib.html#patch-review-process>`_.
Reviews and comments may be submitted by anyone, but a maintainer review is
required to approve *significant* changes.  In addition for significant
changes, there should be at least one reviewer with a sufficient independence
from the author which proposes a new requirement or a change of an existing
requirement.  Working in another company on different projects is sufficiently
independent.  RTEMS maintainers do not know all the details, so they trust in
general people with experience on a certain platform.  Sometimes no review
comments may appear in a reasonable time frame, then an implicit agreement to
the proposed changes is assumed.  Patches can be sent at anytime, so
controlling changes in RTEMS requires a permanent involvement on the RTEMS
developer mailing list.

For a qualification of RTEMS according to certain standards, the requirements
may be approved by an RTEMS user.  The approval by RTEMS users is not the
concern of the RTEMS Project, however, the RTEMS Project should enable RTEMS
users to manage the approval of requirements easily.  This information may be
also used by a independent authority which comes into play with an Independent
Software Verification and Validation (:term:`ISVV`).  It could be used to
select a subset of requirements, e.g. look only at the ones approved by a
certain user.  RTEMS users should be able to reference the determinative
content of requirements, test procedures, test cases and justification reports
in their own documentation.  Changes in the determinative content should
invalidate all references to previous versions.

Add a Requirement
-----------------

.. image:: ../images/eng/req-add.*
    :scale: 70
    :align: center

.. _ReqEngModifyRequirement:

Modify a Requirement
--------------------

.. image:: ../images/eng/req-modify.*
    :scale: 70
    :align: center

Mark a Requirement as Obsolete
------------------------------

Requirements shall be never removed.  They shall be marked as obsolete.  This
ensures that requirement identifiers are not reused.  The procedure to obsolete
a requirement is the same as the one to :ref:`modify a requirement
<ReqEngModifyRequirement>`.

Tooling
=======

Tool Requirements
-----------------

To manage requirements some tool support is helpful.  Here is a list of
requirements for the tool:

* The tool shall be open source.

* The tool should be actively maintained during the initial phase of the RTEMS
  requirements specification.

* The tool shall use plain text storage (no binary formats, no database).

* The tool shall support version control via Git.

* The tool should export the requirements in a human readable form using the
  Sphinx documentation framework.

* The tool shall support traceability of requirements to items external to the
  tool.

* The tool shall support traceability between requirements.

* The tool shall support custom requirement attributes.

* The tool should ensure that there are no cyclic dependencies between
  requirements.

* The tool should provide an export to :term:`ReqIF`.

Tool Evaluation
---------------

During an evaluation phase the following tools were considered:

* `aNimble <https://sourceforge.net/projects/nimble/>`_
* :term:`Doorstop`
* `OSRMT <https://github.com/osrmt/osrmt>`_
* `Papyrus <https://www.eclipse.org/papyrus/>`_
* `ProR <https://www.eclipse.org/rmf/pror/>`_
* `ReqIF Studio <https://formalmind.com/tools/studio/>`_
* `Requirement Heap <https://sourceforge.net/projects/reqheap/>`_
* `rmToo <http://rmtoo.florath.net/>`_

The tools aNimble, OSRMT and Requirement Heap were not selected since they use
a database.  The tools Papyrus, ProR and ReqIF are Eclipse based and use
complex XML files for data storage.  They were difficult to use and lack good
documentation/tutorials.  The tools rmToo and Doorstop turned out to be the
best candidates to manage requirements in the RTEMS Project.  The Doorstop tool
was selected as the first candidate mainly due a recommendation by an RTEMS
user.

.. _ReqEngDoorstop:

Selected Tool - Doorstop
------------------------

:term:`Doorstop` is a requirements management tool.  It has a modern,
object-oriented and well-structured implementation in Python 3.6 under the
LGPLv3 license.  It uses a continuous integration build with style checkers,
static analysis, documentation checks, code coverage, unit test and integration
tests.  In 2019, the project was actively maintained.  Pull requests for minor
improvements and new features were reviewed and integrated within days.  Each
requirement is contained in a single file in :term:`YAML` format.  Requirements
are organized in documents and can be linked to each other
:cite:`Browning:2014:RequirementsManagement`.

Doorstop consists of three main parts

* a stateless command line tool `doorstop`,

* a file format with a pre-defined set of attributes (YAML), and

* a primitive GUI tool (not intended to be used).

For RTEMS, its scope will be extended to manage specifications in general.  The
primary reason for selecting Doorstop as the requirements management tool for
the RTEMS Project is its data format which allows a high degree of
customization.  Doorstop uses a directed, acyclic graph of items.  The items are
files in YAML format.  Each item has a set of
`standard attributes <https://doorstop.readthedocs.io/en/latest/reference/item/>`_
(key-value pairs).

The use case for the standard attributes is requirements management.  However,
Doorstop is capable to manage custom attributes as well.  We will heavily use
custom attributes for the specification items.  Enabling Doorstop to effectively
use custom attributes was done specifically for the RTEMS Project in several
patch sets.

A key feature of Doorstop is the `fingerprint of items
<https://doorstop.readthedocs.io/en/latest/reference/item/#reviewed>`_.
For the RTEMS Project, the fingerprint hash algorithm was changed from MD5 to
SHA256.  In 2019, it can be considered cryptographically secure.  The
fingerprint should cover the normative values of an item, e.g. comments etc. are
not included.  The fingerprint helps RTEMS users to track the significant
changes in the requirements (in contrast to all the changes visible in Git).  As
an example use case, a user may want to assign a project-specific status to
specification items.  This can be done with a table which contains columns for 

1. the UID of the item,

2. the fingerprint, and

3. the project-specific status.

Given the source code of RTEMS (which includes the specification items) and this
table, it can be determined which items are unchanged and which have another
status (e.g. unknown, changed, etc.).
