=================================
Unconstrained Equations of Motion
=================================

.. warning:: This page as not yet been updated for the 2022-2023 course.

.. note::

   You can download this example as a Python script:
   :jupyter-download-script:`eom` or Jupyter Notebook:
   :jupyter-download-notebook:`eom`.

.. jupyter-execute::

   import sympy as sm
   import sympy.physics.mechanics as me
   me.init_vprinting(use_latex='mathjax')

.. container:: invisible

   .. jupyter-execute::

      class ReferenceFrame(me.ReferenceFrame):

          def __init__(self, *args, **kwargs):

              kwargs.pop('latexs', None)

              lab = args[0].lower()
              tex = r'\hat{{{}}}_{}'

              super(ReferenceFrame, self).__init__(*args,
                                                   latexs=(tex.format(lab, 'x'),
                                                           tex.format(lab, 'y'),
                                                           tex.format(lab, 'z')),
                                                   **kwargs)
      me.ReferenceFrame = ReferenceFrame

Dynamical Differential Equations
================================

In the previous chapter, we introduced the generalized active forces
:math:`\bar{F}_r` and the generalized inertia forces :math:`\bar{F}_r^*`.
Together, these two forces give us the *dynamical differential equations*. The
dynamical differential equations for a holonomic system with :math:`p=n`
degrees of freedom in an inertial reference frame. They are a function of the
generalized coordinates, the generalized speeds, the time derivatives of the
generalized speeds, and time. The dynamical differential equations take this
form:

.. math::
   :label: eq-kanes-equations

   \bar{F}_r + \bar{F}^*_r = \bar{f}_d(\dot{\bar{u}}, \bar{u}, \bar{q}, t)  = 0

These are the `Newton-Euler equations`_ for a multibody system in the form
presented in [Kane1985]_ pg. 158, thus we also call these equations *Kane's
Equations*.  The dynamical differential equations can only be formed with
respect to an `inertial reference frame`_, because an inertial reference frame
is one, where Newton's First Law holds, i.e. objects at rest stay at rest
unless an external force acts on them. An inertial reference frame is one that
is not accelerating, or can be assumed not to be with respect to the motion of
the bodies of interest.

.. _Newton-Euler equations: https://en.wikipedia.org/wiki/Newton%E2%80%93Euler_equations
.. _inertial reference frame: https://en.wikipedia.org/wiki/Inertial_frame_of_reference

:math:`\bar{F}^*_r` is always linear in the time derivatives of the generalized
speeds and contains velocity dependent terms such as the centripetal and
Coriolis forces and the rotational velocity coupling terms. These forces are
sometimes call `fictitious forces`_. :math:`\bar{F}_r` are contributing forces
due to body and environment interactions. Texts about dynamics will often
present the dynamical differential equations in this form:

.. math::
   :label: eq-canonical-eom-form

   -\bar{F}^*_r = \bar{F}_r \rightarrow
   \mathbf{M}(\bar{q}, t) \dot{\bar{u}} + \bar{C}(\bar{u}, \bar{q}, t)
   = \bar{F}(\bar{u}, \bar{q}, t)

.. _fictitious forces: https://en.wikipedia.org/wiki/Fictitious_force

where :math:`\mathbf{M}` is called the *mass matrix*,  :math:`\bar{C}` is are
the forces due to the various velocity effects, and :math:`\bar{F}` are the
externally applied forces.

.. todo:: Same something about how M is always invertible and positive definite
   (I think).

Body Fixed Newton-Euler Equations
==================================

To show that Kane's Equations are equivalent to the Newton-Euler equations you
may have seen before, we can find the dynamical differential equations for a
single rigid body using Kane's method and then show the results in the
canonical form. For a rigid body :math:`B` moving in an inertial reference
frame :math:`A` with its velocity and angular velocity expressed in body fixed
coordinates and acted upon by a resultant force :math:`\bar{F}` at the mass
center :math:`B_o` and a moment about the mass center :math:`\bar{M}` we need
these variables, reference frames, and points:

.. jupyter-execute::

   m, Ixx, Iyy, Izz = sm.symbols('m, I_{xx}, I_{yy}, I_{zz}')
   Ixy, Iyz, Ixz = sm.symbols('I_{xy}, I_{yz}, I_{xz}')
   Fx, Fy, Fz, Mx, My, Mz = me.dynamicsymbols('F_x, F_y, F_z, M_x, M_y, M_z')
   u1, u2, u3, u4, u5, u6 = me.dynamicsymbols('u1, u2, u3, u4, u5, u6')

   A = me.ReferenceFrame('A')
   B = me.ReferenceFrame('B')

   Bo = me.Point('Bo')

Now define the angular velocity of the body and the velocity of the mass center
in terms of six generalized coordinates expressed in body fixed coordinates.

.. jupyter-execute::

   A_w_B = u4*B.x + u5*B.y + u6*B.z
   B.set_ang_vel(A, A_w_B)

   A_v_Bo = u1*B.x + u2*B.y + u3*B.z
   Bo.set_vel(A, A_v_Bo)

Now we can find the six partial velocities and partial angular velocities. Note
that we use the ``var_in_dcm=False`` keyword argument. We do this because the
generalized speeds are not present in the unspecified direction cosine matrix
relating :math:`A` and :math:`B`. This allows the derivative in :math:`A` to be
formed without use of a direction cosine matrix. Generalized speeds will never
be present in a direction cosine matrix.

.. jupyter-execute::

   v_Bo_1 = A_v_Bo.diff(u1, A, var_in_dcm=False)
   v_Bo_2 = A_v_Bo.diff(u2, A, var_in_dcm=False)
   v_Bo_3 = A_v_Bo.diff(u3, A, var_in_dcm=False)
   v_Bo_4 = A_v_Bo.diff(u4, A, var_in_dcm=False)
   v_Bo_5 = A_v_Bo.diff(u5, A, var_in_dcm=False)
   v_Bo_6 = A_v_Bo.diff(u6, A, var_in_dcm=False)

   v_Bo_1, v_Bo_2, v_Bo_3, v_Bo_4, v_Bo_5, v_Bo_6

.. jupyter-execute::

   w_B_1 = A_w_B.diff(u1, A, var_in_dcm=False)
   w_B_2 = A_w_B.diff(u2, A, var_in_dcm=False)
   w_B_3 = A_w_B.diff(u3, A, var_in_dcm=False)
   w_B_4 = A_w_B.diff(u4, A, var_in_dcm=False)
   w_B_5 = A_w_B.diff(u5, A, var_in_dcm=False)
   w_B_6 = A_w_B.diff(u6, A, var_in_dcm=False)

   w_B_1, w_B_2, w_B_3, w_B_4, w_B_5, w_B_6

The ``partial_velocity()`` function does this same thing. Notice that due to
our velocity definitions, we get a very simple set of partial velocities.

.. jupyter-execute::

   par_vels = me.partial_velocity([A_v_Bo, A_w_B], [u1, u2, u3, u4, u5, u6], A)

   par_vels

Now form the generalized active forces:

.. jupyter-execute::

   T = Mx*B.x + My*B.y + Mz*B.z
   R = Fx*B.x + Fy*B.y + Fz*B.z

   F1 = v_Bo_1.dot(R) + w_B_1.dot(T)
   F2 = v_Bo_2.dot(R) + w_B_2.dot(T)
   F3 = v_Bo_3.dot(R) + w_B_3.dot(T)
   F4 = v_Bo_4.dot(R) + w_B_4.dot(T)
   F5 = v_Bo_5.dot(R) + w_B_5.dot(T)
   F6 = v_Bo_6.dot(R) + w_B_6.dot(T)

   Fr = sm.Matrix([F1, F2, F3, F4, F4, F6])
   Fr

and the generalized inertia forces:

.. jupyter-execute::

   I = me.inertia(B, Ixx, Iyy, Izz, Ixy, Iyz, Ixz)

   Rs = -m*Bo.acc(A)
   Ts = -(B.ang_acc_in(A).dot(I) + me.cross(A_w_B, I).dot(A_w_B))

   F1s = v_Bo_1.dot(Rs) + w_B_1.dot(Ts)
   F2s = v_Bo_2.dot(Rs) + w_B_2.dot(Ts)
   F3s = v_Bo_3.dot(Rs) + w_B_3.dot(Ts)
   F4s = v_Bo_4.dot(Rs) + w_B_4.dot(Ts)
   F5s = v_Bo_5.dot(Rs) + w_B_5.dot(Ts)
   F6s = v_Bo_6.dot(Rs) + w_B_6.dot(Ts)

   Frs = sm.Matrix([F1s, F2s, F3s, F4s, F5s, F6s])
   Frs

and finally Kane's Equations:

.. jupyter-execute::

   Fr + Frs

We can put Kane's Equations in canonical form (Eq.
:math:numref:`eq-canonical-eom-form`) by extracting the mass matrix, which is
the linear coefficient matrix of :math:`\dot{\bar{u}}`:

.. jupyter-execute::

   u = sm.Matrix([u1, u2, u3, u4, u5, u6])
   t = me.dynamicsymbols._t
   ud = u.diff(t)

The mass matrix is:

.. jupyter-execute::

   M = -Frs.jacobian(ud)
   M

The velocity forces vector is:

.. jupyter-execute::

   C = -Frs.xreplace({udi: 0 for udi in ud})
   C

And the forcing vector is:

.. jupyter-execute::

   F = Fr
   F

This example may seem overly complicated when using Kane's method, but it is a
systematic method that works for any number of rigid bodies and particles in a
system.

Equations of Motion
===================

The kinematical and dynamical differential equations constitute the *equations
of motion* for a holonomic multibody system. These equations are ordinary
differential equations in the generalized speeds and generalized coordinates.

.. math::
   :label: eq-equations-of-motion

   \bar{f}_d(\dot{\bar{u}}, \bar{u}, \bar{q}, t)  = 0 \\
   \bar{f}_k(\dot{\bar{q}}, \bar{u}, \bar{q}, t)  = 0

and since they are both linear in :math:`\dot{\bar{u}}` and
:math:`\dot{\bar{q}}`, respectively, they can be written in a combined form:

.. math::
   :label: eq-intermediate-state-form

   \begin{bmatrix}
   \mathbf{M}_k && 0 \\
   0 && \mathbf{M}_d \\
   \end{bmatrix}
   \begin{bmatrix}
   \dot{\bar{q}} \\
   \dot{\bar{u}}
   \end{bmatrix}
   +
   \begin{bmatrix}
   \bar{g}_k(\bar{u}, \bar{q}, t) \\
   \bar{g}_d(\bar{u}, \bar{q}, t)
   \end{bmatrix}
   =
   \begin{bmatrix}
   0 \\
   0
   \end{bmatrix}

which we write as:

.. math::
   :label: eq-state-form

   \mathbf{M}_m
   \dot{\bar{x}}
   +
   \bar{g}_m
   = \bar{0}

where :math:`\bar{x}=[\bar{q} \quad \bar{u}]^T` is called the *state* of the
system and is comprised of the generalized coordinates and generalized speeds.

Example of Kane's Equations
===========================

Returning to the example from the previous chapter, I will add an additional
particle of mass :math:`m/4` at point :math:`Q` that can slide along the rod
:math:`B` and is attached to point :math:`B_o` via a linear translational
spring with stiffness :math:`k_l` and located by generalized coordinate
:math:`q_3`. The torsional spring stiffness has been renamed to :math:`k_t`.
See :numref:`fig-eom-double-rod-pendulum` for a visual description.

.. _fig-eom-double-rod-pendulum:
.. figure:: figures/eom-double-rod-pendulum.svg
   :align: center
   :width: 600px

   Three dimensional pendulum made up of two pinned rods and a sliding mass on
   rod :math:`B`. Each degree of freedom is resisted by a linear spring. When
   the generalized coordinates are all zero, the two rods are perpendicular to
   each other.

The following code is reproduced from the prior chapter and gives the
velocities and angular velocities of :math:`A_o`, :math:`B_o`, :math:`A`, and
:math:`B` in the inertial reference frame :math:`N`.

.. jupyter-execute::

   m, g, kt, kl, l = sm.symbols('m, g, k_t, k_l, l')
   q1, q2, q3 = me.dynamicsymbols('q1, q2, q3')
   u1, u2, u3 = me.dynamicsymbols('u1, u2, u3')

   N = me.ReferenceFrame('N')
   A = me.ReferenceFrame('A')
   B = me.ReferenceFrame('B')

   A.orient_axis(N, q1, N.z)
   B.orient_axis(A, q2, A.x)

   A.set_ang_vel(N, u1*N.z)
   B.set_ang_vel(A, u2*A.x)

   O = me.Point('O')
   Ao = me.Point('A_O')
   Bo = me.Point('B_O')

   Ao.set_pos(O, l/2*A.x)
   Bo.set_pos(O, l*A.x)

   O.set_vel(N, 0)
   Ao.v2pt_theory(O, N, A)
   Bo.v2pt_theory(O, N, A)

   Ao.vel(N), Bo.vel(N), A.ang_vel_in(N), B.ang_vel_in(N)

We now have the particle at :math:`Q` so we need its velocity for its
contribution to  :math:`F_r` and :math:`F_r^*`. :math:`Q` is moving in
:math:`B` so the one point velocity theorem can be used.

.. jupyter-execute::

   Q = me.Point('Q')
   Q.set_pos(Bo, q3*B.y)
   Q.set_vel(B, u3*B.y)
   Q.v1pt_theory(Bo, N, B)

   Q.vel(N)

We will also need the accelerations of the points and frames for the
generalized inertia forces. For points :math:`A_o`, :math:`B_o` and frames
:math:`A` and :math:`B` these are nicely expressed in terms of
:math:`\dot{\bar{u}}, \bar{u}, \bar{q}`:

.. jupyter-execute::

   Ao.acc(N), Bo.acc(N), A.ang_acc_in(N), B.ang_acc_in(N)

but the acceleration of point :math:`Q` contains :math:`\dot{\bar{q}}` terms,
so we need to eliminate those with the kinematical differential equations:

.. jupyter-execute::

   Q.acc(N)

.. jupyter-execute::

   t = me.dynamicsymbols._t

   qdot_repl = {q1.diff(t): u1,
                q2.diff(t): u2,
                q3.diff(t): u3}

   Q.set_acc(N, Q.acc(N).xreplace(qdot_repl))
   Q.acc(N)

.. warning::
   :class: dropdown

   Be careful when making substitutions when expressions contain derivatives
   and double derivatives. The order in which you make the substitutions matter
   and the printer that SymPy is using may not show you what you think you are
   looking at. Take this expression:

   .. jupyter-execute::

      expr = m*q1.diff(t, 2) + kt*q1.diff(t) + kl*q1
      expr

   Let's say you need to make these substitutions:
   :math:`q_1=\frac{q_2}{q_1},\dot{q}_1=u_1,\ddot{q}_1=\dot{u}_1`. It may seem
   obvious that the :math:`\ddot{q}_1` substitution should be done before
   :math:`q_1`, but care may be needed to help the computer realize this. If
   the highest derivatives are substituted first with successive calls to
   ``.xreplace()`` then you get:

   .. jupyter-execute::

      expr1 = expr.xreplace({q1.diff(t, 2): u1.diff(t)}).xreplace({q1.diff(t): u1}).xreplace({q1: q2/q1})
      expr1

   But if you substitute in the opposite order you get:

   .. jupyter-execute::

      expr2 = expr.xreplace({q1: q2/q1}).xreplace({q1.diff(t): u1}).xreplace({q1.diff(t, 2): u1.diff(t)})
      expr2

   which is a very different answer.

   Checking the ``str()`` or ``srepr()`` versions of the expressions can help
   diagnose what is going on. The string representation of the first expression
   is as expected:

   .. jupyter-execute::

      print(expr1)

   The string representation of the second expression shows that the
   :math:`q_1` symbol was substituted into each derivative term.

   .. jupyter-execute::

      print(expr2)

   ``expr2`` shows different results depending on how you print it! The typeset
   math evaluates the derivatives and the string representation does not.

   If you put all of the substitutions in the same dictionary, SymPy should
   substitute the terms in the expected order:

   .. jupyter-execute::

      expr.xreplace({q1: q2/q1, q1.diff(t): u1, q1.diff(t, 2): u1.diff(t)})

   .. jupyter-execute::

      expr.xreplace({q1.diff(t, 2): u1.diff(t), q1.diff(t): u1, q1: q2/q1})

Now we formulate the resultant forces and torques on each relevant point and
frame:

.. jupyter-execute::

   R_Ao = m*g*N.x
   R_Bo = m*g*N.x + kl*q3*B.y
   R_Q = m/4*g*N.x - kl*q3*B.y
   T_A = -kt*q1*N.z + kt*q2*A.x
   T_B = -kt*q2*A.x

Note the equal and opposite spring forces that act on the pairs of points and
pairs of reference frames. We ignored the reaction torque on :math:`N` from
:math:`A` because :math:`N` is our inertial reference frame.

The inertia dyadics of the two rods are:

.. jupyter-execute::

   I = m*l**2/12
   I_A_Ao = I*me.outer(A.y, A.y) + I*me.outer(A.z, A.z)
   I_B_Bo = I*me.outer(B.x, B.x) + I*me.outer(B.z, B.z)

With all of the necessary elements present for forming :math:`\bar{F}_r` and
:math:`\bar{F}_r^*` we can take advantage of Python for loops to systematically
formulate the generalized forces and inertia forces. Inline comments are added
to help explain the loop.

.. jupyter-execute::

   points = [Ao, Bo, Q]
   forces = [R_Ao, R_Bo, R_Q]
   masses = [m, m, m/4]

   frames = [A, B]
   torques = [T_A, T_B]
   inertias = [I_A_Ao, I_B_Bo]

   Fr_bar = []
   Frs_bar = []

   # loop over the three generalized speeds
   for ur in [u1, u2, u3]:

       # initialize the rth GAF and GIF
       Fr = 0
       Frs = 0

       # for the rth generalized speed, loop though each point to find it's
       # contribution to the generalized forces
       for Pi, Ri, mi in zip(points, forces, masses):
           vr = Pi.vel(N).diff(ur, N)  # rth partial velocity
           Fr += vr.dot(Ri)  # sum in Pi's contribution to GAF
           Rs = -mi*Pi.acc(N)  # rth inertia force
           Frs += vr.dot(Rs)  # sum in Pi's contribution to GIF

       # for the rth generalized speed, loop though each reference frame to find
       # it's contribution to the generalized forces
       for Bi, Ti, Ii in zip(frames, torques, inertias):
           wr = Bi.ang_vel_in(N).diff(ur, N)  # rth partial velocity
           Fr += wr.dot(Ti)  # sum in Bi's contribution to the GIF
           Ts = -(Bi.ang_acc_in(N).dot(Ii) +  # rth inertia torque
                  me.cross(Bi.ang_vel_in(N), Ii).dot(Bi.ang_vel_in(N)))
           Frs += wr.dot(Ts)  # sum in Bi's contribution to the GAF

       Fr_bar.append(Fr)
       Frs_bar.append(Frs)

The generalized forces :math:`\bar{F}_r` are:

.. jupyter-execute::

   Fr = sm.Matrix(Fr_bar)
   Fr

The generalized inertia forces :math:`\bar{F}_r^*` are:

.. jupyter-execute::

   Frs = sm.Matrix(Frs_bar)
   Frs

Notice that the dynamical differential equations are only functions of the time
varying variables :math:`\dot{\bar{u}},\bar{u},\bar{q}`:

.. jupyter-execute::

   me.find_dynamicsymbols(Fr)

.. jupyter-execute::

   me.find_dynamicsymbols(Frs)

Implicit and Explicit Form
==========================

Eq. :math:numref:`eq-state-form` is written in an *implicit form*, meaning that
the derivatives are not explicitly solved for. The *explicit form* is found by
inverting :math:`\mathbf{M}_m`:

.. math::
   :label: eq-state-form-explicit

   \dot{\bar{x}}
   =
   -\mathbf{M}_m^{-1}
   \bar{g}_m
   =\bar{f}_m(\bar{x}, t)

To determine how the state changes over time, these explicit differential
equations can be solved by integrating them with respect to time:

.. math::
   :label: eq-eom-integral

   \bar{x}(t) = \int^{t_f}_{t_0} \bar{f}_m(\bar{x}, t) dt

:math:`\bar{f}_m` is, in general, nonlinear in time, thus analytical solutions
are impossible to find. To solve this integral we must numerically integrate
:math:`\bar{f}_m`. To do so, it will be useful to extract the symbolic forms of
:math:`\mathbf{M}_k`, :math:`\bar{g}_k`, :math:`\mathbf{M}_d`, and
:math:`\bar{g}_d`.

Our example problem has a simple definition of the kinematical differential
equations:

.. math::
   :label: eq-qdot-equals-u

   \begin{bmatrix}
   \dot{q}_1 \\
   \dot{q}_2 \\
   \dot{q}_3
   \end{bmatrix}
   =
   \begin{bmatrix}
   u_1 \\
   u_2 \\
   u_3
   \end{bmatrix}

so :math:`\mathbf{M}_k` is the identity matrix and need not be formed:

.. math::
   :label: eq-yk-identity

   \mathbf{M}_k \dot{\bar{q}} + \bar{g}_k = 0
   \rightarrow
   -
   \begin{bmatrix}
   1 & 0 & 0 \\
   0 & 1 & 0 \\
   0 & 0 & 1 \\
   \end{bmatrix}
   \begin{bmatrix}
   \dot{q}_1 \\
   \dot{q}_2 \\
   \dot{q}_3
   \end{bmatrix}
   +
   \begin{bmatrix}
   u_1 \\
   u_2 \\
   u_3
   \end{bmatrix}
   =
   \begin{bmatrix}
   0 \\
   0 \\
   0
   \end{bmatrix}

But we will need :math:`\mathbf{M}_d` to solve explicitly for
:math:`\dot{\bar{u}}`. Recall that we can use the Jacobian to extract the
linear coefficients of :math:`\dot{\bar{u}}` and then find the terms that
aren't functions of :math:`\dot{\bar{u}}` by substitution (See Sec.
:ref:`Solving Linear Systems`).

Form the column vector :math:`\dot{\bar{u}}`:

.. jupyter-execute::

   u = sm.Matrix([u1, u2, u3])
   ud = u.diff(t)
   ud

Extract the coefficients of :math:`\dot{\bar{u}}`:

.. jupyter-execute::

   Md = Frs.jacobian(ud)
   Md

Make a substitution dictionary to set :math:`\dot{\bar{u}}=\bar{0}`:

.. jupyter-execute::

   ud_zerod = {udr: 0 for udr in ud}
   ud_zerod

Find :math:`\bar{g}_d` with :math:`\bar{g}_d =
\bar{F}_r^* |_{\dot{\bar{u}}=\bar{0}} + \bar{F}_r`:

.. jupyter-execute::

   gd = Frs.xreplace(ud_zerod) + Fr
   gd

Check that neither are functions of :math:`\dot{\bar{u}}`:

.. jupyter-execute::

   me.find_dynamicsymbols(Md)

.. jupyter-execute::

   me.find_dynamicsymbols(gd)
